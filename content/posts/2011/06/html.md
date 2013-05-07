Title: Извлекаем ссылки из HTML страницы
Author: d.rey
Date: 2011-06-27 06:18:00
Slug: html
Tags: extract,url,pyflowctrl,python,html

На основании ссылок строятся алгоритмы ранжирования, построены целые системы обмена трафиком, правила формирования URL положены в основе технологий взаимодействия систем. Умение работать с ссылками очень важно для работы с данными расположенными в Интернет.  Расширим функциональность скрипта из прошлого поста, где рассматривался вопрос [загрузки данных по URL](http://devel.ownport.net/2011/06/url.html) - добавим возможность извлечения ссылок.

Для этой задачи необходимо воспользоваться процессом [UrlExtract](http://code.google.com/p/sources-ownport/source/browse/pyflowctrl/processes/urlextract1.py) ([example10.py](http://code.google.com/p/sources-ownport/source/browse/pyflowctrl/examples/example10.py)). 

    :::python
    from core2 import Stream, Process, ProcessFlow
    from processes.urlfetch1 import UrlFetchGet
    from processes.urlextract1 import UrlExtract
    from processes.stdoutprinter1 import Printer
    from processes.textfilereader1 import TextFileReader
    
    if __name__ == '__main__':
    
        pflow1 = ProcessFlow()
        reader1 = pflow1.new(TextFileReader(path='data/urls'))
        urlfetch1 = pflow1.new(UrlFetchGet())
        #urlextract1 = pflow1.new(UrlExtract(tagged_urls=True))
        urlextract1 = pflow1.new(UrlExtract())
        printer1 = pflow1.new(Printer())
    
        pflow1.link({'sid': reader1, 'son': 'output', 'tid': urlfetch1, 'tin': 'input'})
        pflow1.link({'sid': urlfetch1, 'son': 'output', 'tid': urlextract1, 'tin': 'input'})
        pflow1.link({'sid': urlextract1, 'son': 'output', 'tid': printer1, 'tin': 'input'})
        pflow1.run()
        
Код в виде диаграммы

![](http://4.bp.blogspot.com/-r8z6wRAmZMM/Tgf1G6NPxmI/AAAAAAAAAX4/1PFCJbrlmkc/s1600/UrlExtract.png)

Процесс UrlExtract извлекает и формирует список уникальных URL, содержащихся в документе. Извлекаются ссылки не только из HTML тегов, но из содержимого текста, когда разметка отсутствует. Если необходимо получить список тегированных ссылок, при создании процесса следует определить параметр tagged_urls и установить его в True (UrlExtract(tagged_urls=True)). Тегированные ссылки представляют собой ссылки с дополнительной информацией извлеченной из HTML тегов.

Например:

    :::html
    <a href=”http://www.example.com”>Перейти на example.com</a>

обычная ссылка будет иметь вид: “http://www.example.com”, тегированная “http://www.example.com”: [“Перейти на example.com”](http://www.example.com)

