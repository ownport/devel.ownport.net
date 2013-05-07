Title: pyflowctrl: простой поток логирования
Author: d.rey
Date: 2011-06-13 06:52:00
Slug: pyflowctrl_13
Tags: logging,pyflowctrl,python,stderr,console

В репозиторий [pyflowctrl](http://code.google.com/p/sources-ownport/source/browse/pyflowctrl) добавлен простой поток для логирования данных в стандартный поток ошибок (stderr). С появлением первого потока, отличного от [стандартного Stream](http://code.google.com/p/sources-ownport/source/browse/pyflowctrl/core2.py), изменилась структура файлов репозитории. Добавиласть диретория streams, в которой будут храниться потоки данных - классы, производные от стандартного Stream.

Для активации логирования данных, в списках потоков данных процесса необходимо добавить новый поток - log. Например, для процесса IntGeneratorProcess из [предыдущего примера](http://code.google.com/p/sources-ownport/source/browse/pyflowctrl/examples/example2.py) добавление потока логирования будет выглядеть следующим образом:

    :::python
    class IntGeneratorProcess(Process):
        def __init__(self):
            super(IntGeneratorProcess, self).__init__()
            self.io = {
                'output': Stream(stream_type='output',
                                 data_type='http://en.wikipedia.org/wiki/Integer'),
                'log': None,
            }
            
Изначально имя потока ‘log’ ссылается на None,  но в дальнейшем может ссылаться либо на общий поток логирования либо на выделенный поток логирования для данного класса.

Управление логикой логирования осуществляется из основной программы

    :::python
    if PROCESS_LOGGING:
        consoleLogger = ConsoleLogStream()
        # add log stream to processes
        pflow1.pmap[id_igen].io['log'] = consoleLogger
        pflow1.pmap[id_printer].io['log'] = consoleLogger
        
Добавление логирования в код процесса 

    :::python
    def main(self):
        for i in xrange(10):
            timestamp = datetime.now()
            self.io['output'].put(i)
            if self.io['log']:
                self.io['log'].put('IntGenerator.main().duration: %s' % (datetime.now() - timestamp))
            yield

Пример вывода логирования 

    :::log
    2011-06-12 20:18:01.163051:IntGenerator.main().duration: 0:00:00.000078
    2011-06-12 20:18:01.163131:PrinterProcess.main().duration: 0:00:00.000025
    2011-06-12 20:18:01.163171:IntGenerator.main().duration: 0:00:00.000009
    2011-06-12 20:18:01.163213:PrinterProcess.main().duration: 0:00:00.000014
    2011-06-12 20:18:01.163250:IntGenerator.main().duration: 0:00:00.000009
    2011-06-12 20:18:01.163289:PrinterProcess.main().duration: 0:00:00.000010
    2011-06-12 20:18:01.163325:IntGenerator.main().duration: 0:00:00.000008
    2011-06-12 20:18:01.163363:PrinterProcess.main().duration: 0:00:00.000011
    2011-06-12 20:18:01.163399:IntGenerator.main().duration: 0:00:00.000009
    2011-06-12 20:18:01.163436:PrinterProcess.main().duration: 0:00:00.000010

Полный код примера использования логирования в репозитории [pyflowctrl](http://code.google.com/p/sources-ownport/source/browse/pyflowctrl/examples/example3.py)

