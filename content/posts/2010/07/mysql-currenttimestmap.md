Title: MySQL: как добавить микро/миллисекунды в current_timestmap
Author: d.rey
Date: 2010-07-29 10:14:00
Slug: mysql-currenttimestmap
Tags: timestamp,mysql,python,database

Еще в далеком 2005 году возник [вопрос](http://bugs.mysql.com/bug.php?id=8523) о необходимости представления текущей даты и времени с указанием миллисекунд и микросекунд. Одним из объяснений использования только секунд было: MySQL базируется на SQL92, а согласно этого стандарта функции now(), current_timestamp() должны возвращать время с указанием секунд. Но что делать, если наличие микро или хотя бы миллисекунд все-таки необходимо? Вариантов решений было предложено много. Наверное самым правильным и интересным было решением с использованием С-функции [now_msec()](http://www.xaprb.com/blog/2007/10/30/how-i-built-the-now_usec-udf-for-mysql/). Но недостатком данного решения является необходимость компиляции и добавлении бинарного кода в основной пакет mysql. Если говорить о Windows платформах, то выполнить компиляцию кода не всегда удобно, а порой и невозможно. Если говорить о Unix системах, то данное решение применимо, только когда ваш mysql сервер установлен на выделенном сервере и у вас есть к нему административные права. Но что делать, когда такой возможности нет?

Идея решения данной проблемы - использование результатов функции uuid в mysql. Функция возвращает 5 значений: первые три генерируются из текущего значения времени (timestamp), четвертое значение используется для случаев перехода на зимнее/летнее время и так называемых случаев монотонности времени, последнее - IEEE 802 адрес. Результат имеет вид: 32d07ad9-99fc-11df-8a13-a7466631c030. Для FreeBSD и Linux для значения пятого поля используется MAC адрес, для других операционных систем используется случайное 48 битовое число. В нашем же случае более интересны значения описывающие временные показатели. Согласно [документации](http://www.opengroup.org/onlinepubs/9629399/apdxa.htm) timestamp представляет собой 60 битовое значение счетчика 100 наносекундных интервалов с 00:00:00.00 15 Октября 1582 года. 

Для облегчения индексации, первые три значения необходимо упорядочить. Наиболее часто изменяется первое значение, так как содержит значения долей секунд. Изменив порядок полей, получаем последовательность: <поле3><поле2><поле1>. 

    :::sql
    CREATE FUNCTION uuid2ts() RETURNS CHAR(15)
    BEGIN
    SET @u = uuid();
    RETURN concat(substr(@u,16,3),substr(@u,10,4),substr(@u,1,8));
    END

Результаты выполнения функции 

    :::
    1df9adbb1334c82
    1df9adbb17aacf6
    1df9adbb1be7826

Если провести проверку генерирования timestamp-ов под нагрузкой можно заметить, что формирование уникальных значений в mysql происходит для каждой итерации, в отличии от реализации подобной логики в python c применением модуля uuid и метода uuid1(). В случае с python для выполнения условия уникальности используется четвертый параметр, что не совсем удобно для случаев, когда поле даты и времени используется как версия записи в базе данных.

Преобразование результата функции uuid2ts в значение времени проще выполнить на python

    :::python
    import datetime
    import time
    import uuid
    
    # get offset in seconds between the UUID timestamp Epoch (1582-10-15) and
    # the Epoch used on this computer
    DTD_SECS_DELTA = (datetime.datetime(*time.gmtime(0)[0:3])-datetime.datetime(1582, 10, 15)).days * 86400
    
    def uuid1_to_ts(part_of_uuid1=None):
        """Return a datetime.datetime object that represents the timestamp portion of a uuid1.
    
        Example usage:
    
        print uuid1_to_ts(uuid.uuid1())
        """
        if isinstance(part_of_uuid1, str):
            secs_uuid1 = int(part_of_uuid1,16) / 1e7
        else:
            secs_uuid1 = uuid.uuid1().time / 1e7
        secs_epoch = secs_uuid1 - DTD_SECS_DELTA
        return datetime.datetime.fromtimestamp(secs_epoch)

Результат выполнения функции имеет вид `2010-07-29 09:33:33.711245`

Позавчера появилось [сообщение](http://bugs.mysql.com/bug.php?id=8523): 

    :::text
    [27 Jul 16:18] Rob Young

    Related WL https://intranet.mysql.com/worklog/Server-BackLog/?tid=946 updated so request
    is included in work planned for MySQL 5.6.  GeirH added to notification and will verify
    feasibility.

Так что можно не мудрить и подождать версии 5.6 :)

