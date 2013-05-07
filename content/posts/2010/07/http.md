Title: Маленький Торнадо или асинхронный HTTP сервер
Author: d.rey
Date: 2010-07-13 12:23:00
Slug: http
Tags: server,http,tornado,async,python,request,fast

Несколько месяцев назад я писал о [простом сервере приложений](http://devel.ownport.net/2010/05/blog-post_28.html). Существенным недостатком этого сервера на мой взгляд является относительно медленная работа - у меня никогда не получалось получить более 400 запросов в секунду. Это и стало причиной поиска более быстрого и легкого сервера для приложений. Основные требования к серверу:

- написан на pure python
- ориентирован на работу в доверительном окружении
- обработка порядка 1000 простых запросов в секунду и выше
- отсутствие большого числа зависимостей с другими библиотеками
- работа как в Linux так и Windows окружении
- поддержка HTTP запросов: GET,PUT,POST,DELETE

Оказалось, что найти готовое решение, удовлетворяющее описанным выше требованиям не такая простая задача. Интересным решение стал проект [Tornado](http://www.tornadoweb.org/), open source версия масштабируемого, не блокируемого веб сервера используемого в [FriendFeed](http://friendfeed.com/).  Единственным вопросом использования Tornado в Windows окружении была отсутствие поддержки [epoll](http://www.kernel.org/doc/man-pages/online/pages/man4/epoll.4.html). Альтернативой в Windows является использование select. Для простого приложения Hello World у меня получилось выжать порядка 800 запросов в секунду. Что конечно же является результатом по сравнению с сервером на базе BaseHTTPServer http://docs.python.org/library/basehttpserver.html

Попытка ускорить работу Tornado для работы в Windows привела к созданию достаточно маленького (около 250 строк кода) сервера, удовлетворяющего описанным выше требования. Идеи и часть кода была взята из tornado/httpserver.py и tornado/httputil.py. Что-то было переработано, что-то убрано, что-то добавлено. В качестве замены epoll использован модуль [asyncore](http://docs.python.org/library/asyncore.html). В результате получилось увеличить производительность до порядка 1200 запросов с секунду (в среднем). Для тестирования производительности использовалась утилита ab.exe из пакета [apache](http://httpd.apache.org/docs/2.0/programs/ab.html).

Результат:

    :::text
    D:\apps\network\apache\bin>ab.exe -c 5 -n 10000 http://127.0.0.1:8080/
    This is ApacheBench, Version 2.3 <$Revision: 655654 $>
    Copyright 1996 Adam Twiss, Zeus Technology Ltd, http://www.zeustech.net/
    Licensed to The Apache Software Foundation, http://www.apache.org/
    
    Benchmarking 127.0.0.1 (be patient)
    Completed 1000 requests
    Completed 2000 requests
    Completed 3000 requests
    Completed 4000 requests
    Completed 5000 requests
    Completed 6000 requests
    Completed 7000 requests
    Completed 8000 requests
    Completed 9000 requests
    Completed 10000 requests
    Finished 10000 requests
    
    
    Server Software:        AsyncHTTPServer-0.1
    Server Hostname:        127.0.0.1
    Server Port:            8080
    
    Document Path:          /
    Document Length:        54 bytes
    
    Concurrency Level:      5
    Time taken for tests:   8.594 seconds
    Complete requests:      10000
    Failed requests:        0
    Write errors:           0
    Total transferred:      1240000 bytes
    HTML transferred:       540000 bytes
    Requests per second:    1163.64 [#/sec] (mean)
    Time per request:       4.297 [ms] (mean)
    Time per request:       0.859 [ms] (mean, across all concurrent requests)
    Transfer rate:          140.91 [Kbytes/sec] received
    
    Connection Times (ms)
    min  mean[+/-sd] median   max
    Connect:        0    0   2.1      0      16
    Processing:     0    4   6.8      0      16
    Waiting:        0    3   6.3      0      16
    Total:          0    4   6.9      0      16
    
    Percentage of the requests served within a certain time (ms)
    50%      0
    66%      0
    75%     16
    80%     16
    90%     16
    95%     16
    98%     16
    99%     16
    100%     16 (longest request)
    
    D:\apps\network\apache\bin>
    
В качестве тестового приложения использовался следующий код:

    :::python
    def appl_handler(request):
        message = 'You requested %s via method %s on AsyncHTTPServer-0.1\n' 
        message = message % (request['uri'], request['method'])
    
        header = "HTTP/1.1 200 (OK)\r\nContent-Length: %d\r\nServer: AsyncHTTPServer-0.1\r\n\r\n%s"
        return  header % (len(message), message)
    
    address = ('127.0.0.1', 8080)
    
    print 'HTTP Server %s is starting' % str(address)
    server = HTTPServer(address, appl_handler)
    try:
        server.loop(timeout=2)
    except KeyboardInterrupt:
        server.stop()
        print 'HTTP Server was stopped'

Исходный код сервера доступен на [code.google.com](http://code.google.com/p/sources-ownport/source/browse/parallel/servers/asynchttpsrv.py)
