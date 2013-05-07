Title: Делаем python переносным
Author: d.rey
Date: 2010-11-17 23:20:00
Slug: python
Tags: python,lifehack,portable

В предыдущем посте я упомянул, что для создания cygwin с нуля необходим python для работы скрипта [cygwin-tool.py](http://sources-ownport.googlecode.com/hg/cygwin/cygwin-tool.py). И как-то некрасиво получилось - для того, чтобы установить одно необходимо установить другое. А можно ли использовать python без установки его на компьютер? В сети можно найти несколько сайтов, предлагающий  скачать python не требующий установки. Это и [portable python](http://www.portablepython.com/) и [movable python](http://www.voidspace.org.uk/python/movpy/index.html). Выходит что можно. Если быть откровенным, то у меня на компьютере “установлено” несколько версий python: 2.5, 2.65, 2.7, 3.1. Единственное, что я не использовал для этого ни Portable, ни Movable Python. 

Все гораздо проще. Для того чтобы сделать python переносным, нужно скачать инсталяционный файл с сайта [http://www.python.org](http://www.python.org/). Для примера возьмем python версии 2.7. Загруженный файл: python-2.7.msi. Одно из интересный свойств msi файлов является возможность извлекать содержащиеся в них файлы без установки самого приложения. Сделать это можно стандартной утилитой входящей в состав Windows msiexec.exe. 

    :::text
    msiexec.exe /a python-2.7.msi /qb TARGETDIR=c:/devel/python-2.7
    
В результате на диске с:\ в директории devel/python-2.7 мы имеем вполне рабочую версию python. 

    :::text
    C:\devel\python-2.7>python
    Python 2.7 (r27:82525, Jul  4 2010, 09:01:59) [MSC v.1500 32 bit (Intel)] on win32
    Type "help", "copyright", "credits" or "license" for more information.
    >>> import sys
    >>> sys.version
    '2.7 (r27:82525, Jul  4 2010, 09:01:59) [MSC v.1500 32 bit (Intel)]'
    >>>

Для удобства можно создать файл, определяющий переменные окружения, например: python27env.cmd 

    :::bat
    @echo off
    set PYTHONHOME=c:\devel\python-2.7
    set PATH=%PATH%;c:\devel\python-2.7
    
В заключении скажу, что у меня каких-то сложностей с работой python в таком исполнении не возникало, надеюсь и у вас не возникнет. Пользуйтесь на здоровье. :)
