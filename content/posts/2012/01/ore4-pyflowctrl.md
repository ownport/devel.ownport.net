Title: Новое ядро (сore4) для pyflowctrl
Author: d.rey
Date: 2012-01-08 07:03:00
Slug: ore4-pyflowctrl
Tags: core,pyflowctrl,python

Причины перехода на новое ядро в основном были описаны в предыдущем посте “[Информационные пакеты в pyflowctrl](http://devel.ownport.net/2012/01/pyflowctrl.html)”. Предыдущее ядро работу с пакетами не поддерживало, а плавно перейти на их поддержку не получалось. Кроме этого переход на новое ядро был обусловлен еще и давним желанием вывести работу над pyflowctrl в отдельный проект. Сейчас проект размещен на [code.google.com](http://code.google.com/p/pyflowctrl/). 

В будущем на нем будут публиковаться изменения начиная с ветки core4. С переходом на новую версию изменена структура директорий проекта:

- core4.py перенесен в директорию core4 и `__init__`.py 
- в директорию core4 добавлены под-директории processes/ для хранения кода процессов, streams/ для потоков, tests/ для тестов, examples/ для примеров работы с библиотекой, libs/ для хранения сторонних библиотек.

**Изменения в логике работы core4 от core3 (core4 базируется на core3**):

В классе Process изменены атрибуты доступа к входным/выходным потокам. Сейчас доступ к ним осуществляется с помощью атрибутов input/output, которые нужно явно описать для нового процесса 

    :::python
    class TestProcess(Process):
        def __init__(self):
            super(TestProcess, self).__init__()
            self.input = Stream()
            self.output = Stream()
            
Пример определения основной функции процесса

    :::python
    def main(self):
        while True:
            try:
                packet = self.input.get()
            except EmptyStream:
                yield WAITING
                continue
            # основной код для работы с данными пакета 
            self.output.put(packet)
            yield PROCESSING
            
В классе Stream убрана поддержка описания типизации потоков. В core3 давать описание потоков было не обязательно и зачастую потоки в процессах так и оставались без описаний, так как типизация данных приводила к сложностям соединения процессов. В будущем механизм описания данных в потоках будет доработан. В текущем релизе - это открытый вопрос.

Основной единицей переноса данных между процессами является информационный пакет, класс Packet, см. “[Информационные пакеты в pyflowctrl](http://devel.ownport.net/2012/01/pyflowctrl.html)”

При создании пакета можно передавать “корневые ” переменные, которые он будет содержать 

    :::python
    >>> p = Packet(counter=10, name='test_process')
    >>> p.counter, p.name
    10 test_process
    
В классе ProcessFlow упрощен механизм создания сети взаимодействия процессов - словарь с описанием:
    
    :::python
    network = {
        'processes': {
            'process1': TestProcess(),
            'process2': TestProcess(),
            'printer': Printer(),
        },
        'links': {
            # соединение между process1 и process2
            ('process1.output', 'process2.input', p1p2_handler),
            # соединение между process2 и printer
            ('process2.output', 'printer.input', p2prn_handler),
        }
    }
    
network[‘processes’] описывает процессы, участвующие в работе. Доступ к процессам осуществляется по их именам. 

network[‘links’] описывает взаимодействие между процессами. Каждое соединение - это список из трех аргументов: источник данных, получатель данных, обработчик данных. Источник и получатель данных  - это символьное описание, состоящее из имени процесса и имени потока. Обработчик данных позволяем изменить структуру пакета на этапе передачи его от процесса-источника до процесса-получателя. Это дает возможность адаптировать структуру пакета под нужды следующего процесса. Обработчик данных - это функция на вход которой поступает пакет и на выходе она должна возвращать его же, пример:

    :::python
    def p1p2_handler(packet):
        # process1 -> process2
        packet.set_namespace('test-env1')
        packet['test-env1'].name = packet.name
        packet['test-env1'].url = packet.url
        del packet.name
        del packet.url
        return packet
        
Если данные в обработке не нуждаются, то вместо обработчика данных передается None. 
    
    :::python
    'links': {
        ('process1.output', 'process2.input', None),
    }
    
Обработчики данных можно использовать также как функции-отладчики для вывода данных обменивающихся между процессами.

    :::python
    'links': {
        ('process1.output', 'process2.input', p1p2debug),
    }
    
Загрузка сети network в ProcessFlow выполняется с помощью метода upload(network)

    :::python
    flow = ProcessFlow()
    flow.upload(network)
    for i in xrange(10):
        flow.pmap['process1'].input.put(Packet(counter=i))
    flow.run()
    
Метод run() выполняет запуск сети в работу

Пример работы с core4 расположен в [директории examples репозитория](http://pyflowctrl.googlecode.com/hg/core4/examples/example1.py)
