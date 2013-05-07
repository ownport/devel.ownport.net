Title: Работа с текстовыми файлами
Author: d.rey
Date: 2011-06-24 11:29:00
Slug: blog-post_24
Tags: reader,text,file,pyflowctrl,python

На практике довольно часто приходится работать с текстовыми файлами - это просмотр файлов конфигурации, обработка и анализ логов, статистических отчетов, файлов в [CSV](http://en.wikipedia.org/wiki/Comma-separated_values) или [TSV](http://en.wikipedia.org/wiki/Tab-separated_values) формате. Когда файлы небольшие, то их можно сразу загрузить в память и приступить к обработке. Для случаев, когда файлы имеют размер в несколько сотен мегабайт, рекомендуется выполнять последовательную обработку данных. Чтение порции данных, обработка, чтение новой ...

Принцип последовательной обработки данных лежит в основе [pyflowctrl](http://code.google.com/p/sources-ownport/source/browse/pyflowctrl).  В качестве примера потокового чтения текстовых данных можно использовать [процесс TextFileReader из репозитория](http://code.google.com/p/sources-ownport/source/browse/pyflowctrl/processes/textfilereader1.py), который позволяет выполнять построчное чтения файла

Пример ([example8.py](http://code.google.com/p/sources-ownport/source/browse/pyflowctrl/examples/example8.py)):

    :::python
    from core2 import Stream, Process, ProcessFlow
    from processes.textfilereader1 import TextFileReader
    from processes.stdoutprinter1 import Printer
    
    if __name__ == '__main__':
    
        pflow1 = ProcessFlow()
        reader1 = pflow1.new(TextFileReader(path='core1.py')) 
        printer1 = pflow1.new(Printer()) 
    
        pflow1.link({'sid': reader1, 'son': 'output', 'tid': printer1, 'tin': 'input'})
        pflow1.run()
    
