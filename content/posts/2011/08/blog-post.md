Title: Обработка текста, часть вторая
Author: d.rey
Date: 2011-08-03 06:45:00
Slug: blog-post
Tags: text,pyflowctrl,python,processing

В первой части “[Обработка текста](http://devel.ownport.net/2011/07/blog-post_31.html)” были рассмотрены вопросы разбивки структурированного текста на блоки с использованием разделителей и случаи, когда блоки текста можно выделить с помощью регулярных выражений. Полученные в результате наборы текстовых блоков и имеющие общую структуру представления можно представить в виде словаря используя процесс [Text2Dict](http://code.google.com/p/sources-ownport/source/browse/pyflowctrl/processes/textprocessing1.py)

В качестве примера используем текстовый блок (block) содержащий следующую информацию: 

    :::text
    POST /xmlrpc.php HTTP/1.1
    User-Agent: wp-iphone/1.0
    Content-Type: text/xml
    Content-Length: 2963
    Accept: */*
    Accept-Language: en-us
    Accept-Encoding: gzip, deflate
    Connection: keep-alive
    Host: example.com

Для удобства дальнейшей обработки преобразуем этот блок в словарь параметров с помощью [Text2Dict](http://code.google.com/p/sources-ownport/source/browse/pyflowctrl/processes/textprocessing1.py) 

    :::python
    from pprint import pprint
    from processes.textprocessing1 import Text2Dict
    
    dict_selectors = {
        'method':           r'(?m)^(\w+) .+? HTTP/\d\.\d$',
        'url':              r'(?m)^\w+ (.+?) HTTP/\d\.\d$',
        'http':             r'(?m)^\w+ .+? (HTTP/\d\.\d)$',
        'user-agent':       r'(?m)^User-Agent: (.+?)$',
        'content-type':     r'(?m)^Content-Type: (.+?)$',
        'content-length':   r'(?m)^Content-Length: (\d+)$',
        'accept':           r'(?m)^Accept: (.+?)$',
        'accept-language':  r'(?m)^Accept-Language: (.+?)$',
        'accept-encoding':  r'(?m)^Accept-Encoding: (.+?)$',
        'connection':       r'(?m)^Connection: (.+?)$',
        'host':             r'(?m)^Host: (.+?)$',
    }
    
    t2d = Text2Dict(selectors=dict_selectors)
    pprint(t2d.run_once(('block#1',block)))
    
В качестве параметра для процесса Text2Dict передается словарь dict_selectors на основании которого производится выборка. Ключи словаря - это имена переменных, значение словаря - регулярное выражение на основании которого делается выборка текста. Выборка выполняется с помощью метода re.findall, поэтому, если в тексте содержится несколько блоков удовлетворяющих регулярному выражению, Text2Dict вернет список значений.

Метод run_once() с указанными параметрами (‘block#1’, block) позволяет передать на вход процесса исходные данные, выполнить одну итерацию и вернуть результат. Без указанных параметров код выглядел бы следующим образом: 
    
    :::python
    t2d.io['input'].put(('block#1', block))
    t2d.run_once()
    pprint(t2d.io['output'].get())
    
В результате выполнения скрипта, получаем словарь параметров текстового блока 

    :::python
    ('block#1',
        {'accept': '*/*',
        'accept-encoding': 'gzip, deflate',
        'accept-language': 'en-us',
        'connection': 'keep-alive',
        'content-length': '2963',
        'content-type': 'text/xml',
        'host': 'example.com',
        'http': 'HTTP/1.1',
        'method': 'POST',
        'url': '/xmlrpc.php',
        'user-agent': 'wp-iphone/1.0'})
        
