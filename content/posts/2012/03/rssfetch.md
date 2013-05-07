Title: rssfetch, новые возможности
Author: d.rey
Date: 2012-03-11 08:38:00
Slug: rssfetch
Tags: kvlite2,fetch,daemon,python,feedparser,rss

Со времени публикации последнего поста “[Загрузчик RSS лент (rssfetch)](http://devel.ownport.net/2012/03/rss-rssfetch.html)”, rssfetch обзавелся рядом дополнений и ноововведений:

- изменена структура помощи и работа с командной строкой
- добавлены функции логирования работы с возможностью вывода как на консоль, так и запись в файл
- добавлена возможность запуска как сервиса

Для вызова помощи достаточно указать аргумент -h или --help

    :::text
    $ python rssfetch.py --help
    usage: rssfecth <options>
    author: Andrey Usov <http://devel.ownport.net>, version: 0.2
    Options:
     -h, --help   print this help
     -i, --import=<json_file> import feeds from json file
     -s, --show_feeds  show feeds details
     -l, --logfile   logging to file
     -u, --update_entries  run periodical feed entries update
     -d, --daemon   run periodical feed entries update as service
     
Импорт новых RSS лент и вывод детальной информации о них не изменился, изменились только аргументы командной строки. Теперь поддерживаются короткие ключи.

Ранее логирование выполнялось только на стандартный вывод, сейчас при указании ключа -i или --logfile вывод сообщений можно направить в файл
    :::text
    $ python rssfetch.py -u --logfile=rssfetch.log
    
Запуск обновлений постов можно выполнять двумя путями: как приложение или как сервис. При запуске как приложения, rssfetch будет выполняться свою работу до тех пор пока окно в котором он запущен rssfetch не будет закрыто или пока пользователь сам не остановит его работу нажатие Ctrl+C. 

    :::text
    $ python rssfetch.py -u 
    
При запуске rssfetсh как сервис такая зависимость исчезает. 
    
    :::text
    $ python rssfetch.py -u -d -l rssfetch.log 
    
Для того, чтобы запустить rssfetch как сервис необходимо в файле конфигурации settings.py прописать абсолютный путь к PID файлу - тестовый файл содержащий номер процесса rssfetch, формируется автоматически при запуске.

Для остановки процесса необходимо выполнить
    
    :::text
    $kill `cat rssfetch.pid`
    
Иногда не совсем удобно, когда запускаемые файлы  и библиотеки для работы с приложением расположены в одной директории. Проще работать с один файлом. Тогда его можно перенести в bin директорию не боясь создать мешанину из скриптов и библиотек. Python поддерживает запуск приложения прямо из zip файла. Выполнив настройки rssfetch в settings.py файле можно запаковать все необходимые файлы в один архив rssfetch.zip. Список файлов ниже:
    
    :::text
    feedparser.py
    kvlite2.py
    rssfetch.py
    settings.py
    __main__.py
    
Теперь rssfetch можно запустить прямо из zip файла
    
    :::text
    $ python rssfetch.zip --help
    
    usage: rssfecth <options>
    author: Andrey Usov <http://devel.ownport.net>, version: 0.2
    Options:
     -h, --help   print this help
     -i, --import=<json_file> import feeds from json file
     -s, --show_feeds  show feeds details
     -l, --logfile   logging to file
     -u, --update_entries  run periodical feed entries update
     -d, --daemon   run periodical feed entries update as service
     
Загрузить версию rssfetch 0.2 можно с [http://code.google.com/p/sources-ownport/](http://code.google.com/p/sources-ownport/downloads/list)
Исходный код доступен в репозиториии [http://code.google.com/p/sources-ownport/](http://code.google.com/p/sources-ownport/source/browse/rssfetch)

