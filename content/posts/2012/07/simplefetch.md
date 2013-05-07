Title: Новый проект simplefetch
Author: d.rey
Date: 2012-07-18 08:55:00
Slug: simplefetch
Tags: simplefetch,url,fetch,python

Проект [simplefetch](https://github.com/ownport/simplefetch) появился как небольшое ответвление от [urlfetch](https://github.com/lyxint/urlfetch), своего рода экспериментальная площадка для тестирования подходов, отличных от принятых в urlfetch. Несмотря на это, simplefetch уже сейчас достаточно самостоятелен и работоспособен. Поддержи сессий как у urlfetch нет, но есть поддержка proxy, обработка сжатых данных (deflate, gzip), возможность удерживать соединение с хостом для отправки запросов без необходимости повтороной переустановки соединения. Будет хорошо, если нарабоки simplefetch будут приняты и в urlfetch :)

**Краткий обзор функциональности**

simplefetch - это библиотека, основная задача которой упростить организацию HTTP запросов. В настоящий момент существует достаточно большое количество мощных и функциональных библиотек, например [requests](https://github.com/kennethreitz/requests). Идея simplefetch собрать простоту и удобство работы с HTTP запросами в небольшой библиотеке, состоящей всего из одного единственного файла, без зависимостей с другими библиотеками, кроме тех, что входят стандартный пакет python.

**Примеры использования**

simplefetch сожержит несколько алиасов к основным HTTP запросам, поэтому GET запрос на http://devel.ownport.net выглядит достаточно просто:

    :::python
    >>> import simplefetch
    >>> resp  = simplefetch.get('http://devel.ownport.net')
    >>> resp.headers
    { 'transfer-encoding': 'chunked', 'expires': 'Wed, 18 Jul 2012 04:58:49 GMT', 'server': 'GSE', 'last-modified': 'Wed, 11 Jul 2012 05:51:27 GMT', 'connection': 'Keep-Alive', 'etag': '"1fc3cfe5-7483-4765-8f67-eee40b813abc"', 'cache-control': 'private, max-age=0', 'date': 'Wed, 18 Jul 2012 04:58:49 GMT', 'content-type': 'text/html; charset=UTF-8' }
    >>> len(resp.content)
    86641
    
Если необходимо удерживать соединение с хостом 

    :::python
    >>> conn = simplefetch.Connection(scheme='http', host='devel.ownport.net')
    >>> conn.request('GET', '/', None, {} )
    >>> resp = conn.response()
    >>> resp.headers
    {'transfer-encoding': 'chunked', 'expires': 'Wed, 18 Jul 2012 05:11:54 GMT', 'server': 'GSE', 'last-modified': 'Wed, 11 Jul 2012 05:51:27 GMT', 'connection': 'Keep-Alive', 'etag': '"1fc3cfe5-7483-4765-8f67-eee40b813abc"', 'cache-control': 'private, max-age=0', 'date': 'Wed, 18 Jul 2012 05:11:54 GMT', 'content-type': 'text/html; charset=UTF-8'}
    >>> len(resp.content)
    86641
    >>> conn.request('GET', '/search/label/python', None, {} )
    >>> resp = conn.response()
    >>> resp.headers
    {'transfer-encoding': 'chunked', 'expires': 'Wed, 18 Jul 2012 05:21:02 GMT', 'server': 'GSE', 'last-modified': 'Wed, 11 Jul 2012 05:51:27 GMT', 'connection': 'Keep-Alive', 'etag': '"1fc3cfe5-7483-4765-8f67-eee40b813abc"', 'cache-control': 'private, max-age=0', 'date': 'Wed, 18 Jul 2012 05:21:02 GMT', 'content-type': 'text/html; charset=UTF-8'}
    >>> len(resp.content)
    127584
    
Simplefetch автоматически определяет переменные окружения для прокси, поэтому в случае использования функций-алиасов к методам GET, HEAD, POST, … никаких дополнительных операций выполнять не нужно. Для работы через соединения, процедура формирования запроса несколько отличается. Нужно только определить какую схему использовать при установлении соединения, а аргумент host можно не указывать. simplefetch автоматически подставит значения хостов их переменных окружения для соответствующего прокси. При формировании запроса необходимо указать полный URL 
    
    :::python
    >>> conn = simplefetch.Connection(scheme='http')
    >>> conn.request('GET', 'http://devel.ownport.net', None, {} )
    >>> resp = conn.response()
    >>> resp.headers
    {'via': '1.1 PROXY, 'proxy-connection': 'Keep-Alive', 'x-content-type-options': 'nosniff', 'transfer-encoding': 'chunked', 'expires': 'Wed, 18 Jul 2012 05:37:59 GMT', 'server': 'GSE', 'last-modified': 'Wed, 11 Jul 2012 05:51:27 GMT', 'connection': 'Keep-Alive', 'etag': '"1fc3cfe5-7483-4765-8f67-eee40b813abc"', 'cache-control': 'private, max-age=0', 'date': 'Wed, 18 Jul 2012 05:37:59 GMT', 'content-type': 'text/html; charset=UTF-8', 'x-xss-protection': '1; mode=block'}
    >>> len(resp.content)
    86641
    
Заголовки запросов можно передавать через переменную headers в виде словаря 

    :::python
    >>> simplefetch.get('http://devel.ownport.net', headers={'User-Agent': 'simplefetch/0.3.2'})
    
А можно и с помощью класса Headers. Пример использования базовой аутентификации 
    
    :::python
    >>> headers = simplefetch.Headers()
    >>> headers.basic_auth('username', 'password')
    >>> simplefetch.get('http://www.example.com', headers=headers.items())
    
