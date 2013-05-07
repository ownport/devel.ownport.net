Title: Обработка текста, часть первая
Author: d.rey
Date: 2011-07-31 07:52:00
Slug: blog-post_31
Tags: text,pyflowctrl,python,processing

В продолжении темы работы с текстовыми файлами стоит рассмотреть несколько подходов обработки структурированных текстовых данных. Наиболее известные форматы представления данных: HTML, XML, JSON, - форматы, использующие специальную разметку. Для многих из них существуют специальные библиотеки для парсинга и обработки. Существую также форматы, в которых данные не содержат разметки, а представлены в виде некоторой текстовой структуры. К таким данным относятся лог-файлы, вывод аварийных сообщений, дампы системы, статистические отчеты в текстовом формате, результаты мониторинга и многое другое.

Зачастую данные представленные в виде некоторой структуры повторяются для описания различных событий внутри одного текстового файла. В данном случае для извлечения структур данных можно воспользоваться двумя процессами TextSplit и TextSelector из небольшой библиотеки [textprocessing](http://code.google.com/p/sources-ownport/source/browse/pyflowctrl/processes/textprocessing1.py)  для [pyflowctrl](http://code.google.com/p/sources-ownport/source/browse/pyflowctrl)

**TextSplit** 

позволяет данные поступающие во входной поток разделять на основании некоторого разделителя. В качестве разделителя может выступать как один символ, так и несколько. Регулярные выражения не поддерживаются. Например, в текстовом файле сообщения разделены двойным [EOL (end of line)](http://en.wikipedia.org/wiki/Newline) ‘\n\n’ 

    :::python
    from processes.filereader1 import FileReader
    from processes.textprocessing1 import TextSplitter 
    from processes.stdoutprinter1 import Printer
    
    flow = ProcessFlow()
    id_reader = flow.new(FileReader())
    id_tsplit = flow.new(TextSplitter(separator='\n\n'))
    id_print = flow.new(Printer())
    
    # FileReader -> TextSplitter 
    flow.link({'sid': id_reader, 'son': 'output', 'tid': id_tsplit, 'tin': 'input'})
    # TextSplitter -> Printer
    flow.link({'sid': id_tsplit, 'son': 'output', 'tid': id_print, 'tin': 'input'})
    
    flow.pmap[id_reader].io['input'].put('/media/data/traces/wireshark/camel.log')
    flow.run()
    
**TextSelector**

позволяет извлекать из текста данные, на основании регулярных выражений. Может быть удобен в случаях, когда из текстовых данных нужно выбрать определенный блок данных. Можно выполнить разделение однотипных данных, поставив каждому выбранному блоку в соответствие свой поток.  Например, есть HTML страница содержащая информацию о погоде. Вместо парсинга всего документа можно выделить только данные, относящиеся к погоде и заключенные в `<div class="weather-info">` и `</div>` 

    :::python
    from processes.filereader1 import FileReader
    from processes.textprocessing1 import TextSelector
    from processes.stdoutprinter1 import Printer
    
    weather_info_selectors = {
        'weather-info': r'<div class="weather-info">(.+?)</div>',
    }
    
    flow = ProcessFlow()
    id_reader = flow.new(FileReader())
    id_tselect = flow.new(TextSelector(selectors=weather_info_selectors))
    id_print = flow.new(Printer())
    
    # FileReader -> TextSelector
    flow.link({'sid': id_reader, 'son': 'output', 'tid': id_tselect, 'tin': 'input'})
    # TextSelector -> Printer
    flow.link({'sid': id_tselect, 'son': 'weather-info', 'tid': id_print, 'tin': 'input'})
    
    flow.pmap[id_reader].io['input'].put('/media/data/weather/weather-info.20110731.html')
    flow.run()
    
