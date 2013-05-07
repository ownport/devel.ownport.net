Title: Управление bottle.py как сервисом
Author: d.rey
Date: 2012-09-06 08:09:00
Slug: bottlepy
Tags: service,bottle,daemon,framework,python,pyservice,web

[Bottle](http://bottlepy.org/) - это быстрый и простой микро-фреймворк для небольших web приложений. Несмотря на его небольшие размеры (всего один файл) в него включено:

- request dispatching (URL routing) с поддержкой URL параметров
- поддержка шаблонов 
- встроенный HTTP сервер
- поддержка адаптеров для большого числа WSGI/HTTP-серверов и систем шаблонов
- нет внешних зависимостей с другими модулями, кроме Python Standard Library 

В виду малых размеров bottle.py удобно использоваться в качестве development сервера или как основу для небольшого фронтеда, внутреннего сервиса не ориентированного на большие нагрузки. Из коробки встроенный HTTP сервер bottle.py нельзя запустить как сервис и нужно постоянно держать окно консоли открытым. Для того, чтобы была возможность запустить сервер в фоне, можно воспользоваться небольшой библиотекой [pyservice](https://github.com/ownport/pyservice), о которой упоминалось несколько постов назад.

Пример кода расположен на [githib](https://github.com/ownport/pyservice/tree/master/examples/bottlepy-daemon). bottled.py позволяет запустить встроенный в bottle.py сервер как сервис. После запуска сервиса по адресу http://127.0.0.1:8080 будет доступна тестовая страничка BottleProcess. Данный пример можно использовать как основу для web приложения. Как разрабатывать приложения на базе bottle.py можно найти на странице [проекта](http://bottlepy.org/).

Благодаря pyservice существует два способа запуска сервиса: либо через консоль pyservice либо запуск непосредственно через bottled.py

    :::text
    $ python -m packages.pysevice bottled.BottleProcess
    usage: pyservice [-v] [-h] [process] [{start,stop,restart,status}]
    
    positional arguments:
      process               process class path to run (modulename.ProcessClass) or
                            configuration file path to use (/path/to/config.py)
      {start,stop,restart,status}
    
    optional arguments:
      -v, --version         show program's version number and exit
      -h, --help            show program's help text and exit
    
Или

    :::text
    $ python bottled.py 
    usage: bottled <start,stop,restart,status>
    
Дополнительно в bottled.py включено перенаправление логов в файл, формат лог файла несколько видоизменен, относительно оригинального формата bottle.py - убраны лишние поля с датой. 

