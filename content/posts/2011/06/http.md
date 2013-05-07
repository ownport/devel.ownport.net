Title: Поддержка различных HTTP методов
Author: d.rey
Date: 2011-06-28 07:35:00
Slug: http
Tags: http,url,fetch,pyflowctrl,python

Согласно [Wikipedia](http://ru.wikipedia.org/wiki/HTTP): “Основой HTTP является технология «клиент-сервер», то есть предполагается существование потребителей (клиентов), которые инициируют соединение и посылают запрос, и поставщиков (серверов), которые ожидают соединения для получения запроса, производят необходимые действия и возвращают обратно сообщение с результатом... Метод HTTP - последовательность из любых символов, кроме управляющих и разделителей, указывающая на основную операцию над ресурсом”. HTTP определяет 9 методов: HEAD, GET, POST, PUT, DELETE, TRACE, OPTIONS,CONNECT и PATH. Процесс UrlFetchGet из поста “[Получение данных по URL](http://devel.ownport.net/2011/06/url.html)” поддерживал только один метод - GET. Новый процесс снимает эти ограничения.

Процесс [UrlFetch](http://code.google.com/p/sources-ownport/source/browse/pyflowctrl/processes/urlfetch2.py) позволяет работать с различными методами. В настоящий момент на практике проверена работа наиболее часто используемых методов GET и POST, а также менее используемого HEAD. В входной поток могут передавать:

`URL`, в этом случае все запросы к серверу будут осуществляться с запросом GET. 

    :::python
    data  = (
        'http://example.com/page/1/',
        'http://example.com/page/2/',
        'http://example.com/page/3/'
    )
    
параметры запроса в виде списка: URL, метод, данные, заголовки.

    :::python
    data  = (
        ('http://example.com/page/1/', 'GET'),
        ('http://example.com/page/1/', 'GET', None, { 'User-Agent' : 'Mozilla/5.0 (compatible)' }),
        ('http://example.com/search', 'POST', {'s':'examples', 'id':'1'}),
    )
    
Пример кода 

    :::python
    from processes.urlfetch2 import UrlFetch
    urlfetch = UrlFetch()
    for d in data:
        print d
        urlfetch.io['input'].put(d)
        urlfetch.run_once()
        try:
            (url, status_code, content, info) = urlfetch.io['output'].get()
            print url, status_code, len(content), info
        except:
            pass
        print
        
Выходной поток не отличаются от своего предшественника [UrlFetchGet](http://devel.ownport.net/2011/06/url.html), на выходе для каждого запроса получаем список из:
- `URL`, не обязательно соответствующий входному URL (для случая redirection)
- `STATUS_CODE`, результат выполнения, соответствующий одному из HTTP кодов, если значение положительное. И если значение отрицательное, проблема указывается в поле error-msg для INFO. Например, для случая 'Couldn't resolve host'
- `CONTENT`, контент соответствующий входному URL
- `INFO`, дополнительная информация возвращенная сервером: Content-type, Cookie, Last-Modified, Server и т.д.

