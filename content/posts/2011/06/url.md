Title: Получение данных по URL
Author: d.rey
Date: 2011-06-26 05:43:00
Slug: url
Tags: url,fetch,pyflowctrl,python

В настоящее время работа с данными из Сети уже не просто удобная возможность, сейчас это то, что должно работать по умолчанию. В Сети находится огромное количество информации: анонсы фильмов, каталоги товаров, прогноз погоды и многое другое. Лишать себя этих благ было бы как-то не разумно. С учетом того, что большая часть информации находится на веб серверах, рассмотрим возможно загрузки данных из Сети по URL

В репозитории [pyflowctrl](http://code.google.com/p/sources-ownport/source/browse/pyflowctrl) для этого есть специальный процесс [UrlFetchGet](http://code.google.com/p/sources-ownport/source/browse/pyflowctrl/processes/urlfetch1.py), который с помощью HTTP GET запроса загружает данные.

Пример простого скрипта [example9.py](http://code.google.com/p/sources-ownport/source/browse/pyflowctrl/examples/example9.py), который на основании списка URL, полученного из текстового файла, загружает контент с веб сервера и выводит его на экран консоли. 

    :::python
    from core2 import Stream, Process, ProcessFlow
    from processes.urlfetch1 import UrlFetchGet
    from processes.stdoutprinter1 import Printer
    from processes.textfilereader1 import TextFileReader
    
    if __name__ == '__main__':
    
        pflow1 = ProcessFlow()
        reader1 = pflow1.new(TextFileReader(path='data/urls'))
        urlfetch1 = pflow1.new(UrlFetchGet())
        printer1 = pflow1.new(Printer())
    
        pflow1.link({'sid': reader1, 'son': 'output', 'tid': urlfetch1, 'tin': 'input'})
        pflow1.link({'sid': urlfetch1, 'son': 'output', 'tid': printer1, 'tin': 'input'})
        pflow1.run()

Про процессы TextFileReader и Printer подробнее можно прочесть в “[Работа с текстовыми файлами](http://devel.ownport.net/2011/06/blog-post_24.html)”

Схематически код приведенный выше можно представить в виде диаграммы

![](http://4.bp.blogspot.com/-Vmlucw-n3cg/TgabxzRcFeI/AAAAAAAAAXo/AtF9wVlxpk0/s1600/UrlFetch.png)

На вход процесса UrlFetchGet поступают URL ресурсов c которых необходимо получить данные.  На выходе, для каждого запроса получаем список из:

- `URL`, не обязательно соответствующий входному URL (для случая redirection)
- `STATUS_CODE`, результат выполнения, соответствующий одному из HTTP кодов, если значение положительное. И если значение отрицательное, проблема указывается в поле error-msg для INFO. Например, для случая ‘Couldn't resolve host’
- `CONTENT`, контент соответствующий входному URL 
- `INFO`, дополнительная информация возвращенная сервером: Content-type, Cookie, Last-Modified, Server и т.д.

