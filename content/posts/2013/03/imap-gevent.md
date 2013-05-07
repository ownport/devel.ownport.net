Title: Неблокируемая imap функция для gevent библиотеки
Author: d.rey
Date: 2013-03-10 13:13:00
Slug: imap-gevent
Tags: non-blocking,python,imap,gevent

В состав библиотеки [gevent](http://www.gevent.org/) для работы с группой или пулом greenlet-ов входят функции [imap](http://www.gevent.org/gevent.pool.html#gevent.pool.Group.imap) и [imap_unordered](http://www.gevent.org/gevent.pool.html#gevent.pool.Group.imap_unordered). Функции удобны для распараллеливания однотипных сетевых задач, таких как резолвинг DNS имен, получение данных RSS лент и т.д. Для случаев, когда необходимо в параллель с работой пула или группы greenlet-ов выполнять обработку уже полученных данных или иметь возможность добавлять в очередь на обработку новые исходные данные, стандартные функции не всегда могут быть удобны.

Небольшая функция imap_unblocking позволяет устранить описанные выше неудобства при работе с gevent библиотекой.

    :::python
    import urllib2
    from gevent import monkey
    monkey.patch_socket()
            
    def urlfetch(url):
        return "%s:%d" % (url, len(urllib2.urlopen(url).read()))
    
    queue = Queue()
    urls = [
        'http://www.google.com','http://www.yahoo.com','http://www.yandex.ru',
        'http://www.wired.com','http://python.org/','http://www.ubuntu.com/',
        'http://www.apple.com/',]
    for url in urls:
        queue.put(url)
    
    imap = imap_nonblocking(3, urlfetch, queue)
    result = [r for r in imap if r is not None]
    
Функция imap_unblocking представляет собой генератор, возвращающий данные по мере их доступности. В случае, если на момент обращения данных еще нет, возвращается значение None. Когда функция imap_nonblocking завершает работу, срабатывает исключение StopIteration.

Исходный код функции imap_unblocking доступен на [github.com/ownport/pysnippets](https://github.com/ownport/pysnippets/blob/master/nonblockingimap.py)

