Title: Загрузчик RSS лент (rssfetch)
Author: d.rey
Date: 2012-03-09 09:22:00
Slug: rss-rssfetch
Tags: kvlite2,fetch,python,feedparser,rss

Данным постом начинается небольшая серия статей по созданию загрузчика RSS лент с использованием [Universal Feed Parser](http://code.google.com/p/feedparser/) и [KVLite2](http://code.google.com/p/kvlite/). Основная задача: периодическая проверка наличия новых постов для выбранных RSS лент и хранение их в базе данных для последующей обработки. 

Для начала работы необходимо создать новую базу данных в MySQL - rssfetch и пользователя, который будет работать с этой базой данных - rssfetch.

    :::mysql
    mysql> create database rssfetch;
    Query OK, 1 row affected (0.03 sec)
    mysql> grant all privileges on rssfetch.* to 'rssfetch'@'localhost' identified by '<ваш пароль>';
    Query OK, 0 rows affected (0.10 sec)
    
Название базы данных и имя пользователя могут быть произвольными. “rssfetch” взят как пример.

Для созданной базы данных и пользователя необходимо прописать настройки в файле конфигурации settings.py 

    :::text
    FEEDS_URI = "mysql://rssfetch:<ваш пароль>@localhost/rssfetch.feeds"
    ENTRIES_URI = "mysql://rssfetch:<ваш пароль>@localhost/rssfetch.entries"
    USER_AGENT = 'rssfetcher/0.1'
    SERVER_RESPONSE_TIMEOUT = 60    # time for waiting response from the server
    TIME_INTERVAL = 1800 # seconds, 30 minutes. Waiting time for the next update
    
- FEEDS_URI и ENTRIES_FEED параметры описывают параметры доступа к коллекциям RSS лент и постам.
- USER_AGENT параметр позволяет изменить значение по умолчанию, определяемое в feedparser [http://ru.wikipedia.org/wiki/User_Agent](http://ru.wikipedia.org/wiki/User_Agent)</li><li>SERVER_RESPONSE_TIMEOUT определяет в секундах время ожидания ответа от сервера</li><li>TIME_INTERVAL определяет в секундах время между циклами обновления RSS лент

Для начала работы rssfetch необходимо загрузить информацию о RSS лентах. Загрузка выполняется из JSON файла: 

    :::text
    # python rssfetch.py import <json_file>

Формат файла имеет следующую структуру: 

    :::python
    [
        {   "title": "MySQL Performance Blog",         
            "text": "MySQL Performance Blog. Percona's MySQL & InnoDB performance and scalability blog",         
            "xmlUrl": "http://www.mysqlperformanceblog.com/feed/",
            "htmlUrl": "http://www.mysqlperformanceblog.com/" }
    ]
    
Обязательным полем является xmlUrl, указывающее на RSS ленту. Оно определяет уникальность записей в базе данных. Повторная попытка импортировать в базу новую RSS ленту с уже существующим xmlUrl будет проигнорирована. Остальные поля являются вспомогательными и не влияют на работу rssfetch. Описание ленты не ограничено только этими полями, можно добавлять любые поля по необходимости. Данные по RSS лентам хранятся в таблице rssfetch.feeds

Для вывода списка импортированных  RSS лент 

    :::text
    # python rssfetch.py show_feeds
    ...
    id: 108, key: fb4805703b327adbd176377a5fba6b6b3e129c8c
    details: {u'xmlUrl': u'http://www.mysqlperformanceblog.com/feed/', u'text': u"MySQL Performance Blog. Percona's MySQL & InnoDB performance and scalability blog", u'htmlUrl': u'http://www.mysqlperformanceblog.com/', u'title': u'MySQL Performance Blog'}
    ...
    
- `id` - порядковый номер RSS ленты в базе данных,
- `key` - идентификатор RSS ленты, рассчитывается как функция sha1 от поля xmlUrl  hashlib.sha1(feed['xmlUrl']).hexdigest()
- `details` - детальное описание RSS ленты

Обновление данных для RSS лент выполняется с помощью команды 

    :::
    # python rssfetch.py update_entries
    …
    2012-03-09 08:02:22 feed: http://www.mysqlperformanceblog.com/feed/ (total: 10, new: 0)
    …

После выполнения команды запускается периодический процесс обновления данных RSS лент в порядке их импорта в базу данных. Периодичность проверки новых постов определяется параметром settings.TIME_INTERVAL. Лог выполнения пишется в стандартный вывод. Для каждой ленты отображается краткая информация: дата и время обновления, ссылка на RSS ленту, общее число загруженных постов и количество новых. 

rssfetch умеет оперировать значением [ETag](http://en.wikipedia.org/wiki/HTTP_ETag), позволяя тем самым более эффективно использовать ресурсы как сервера, так и пропускной способности канала между сервером и rssfetch. Значение ETag сохраняется после каждого обращения к серверу и при повторном обращении передается в запросе. Для корректной работы этой функциональности сервер должен уметь работать с полем ETag.

По окончании каждого цикла в лог записывает общая информация о количестве проверенных лент, числе новых записей и общем времени обновления. 

    :::log
    2012-03-09 08:02:22 rssfetch: total feeds: 109, new entries: 2, feeds update time 0:03:10.687371

Загрузить версию rssfetch 0.1 можно с [http://code.google.com/p/sources-ownport/](http://code.google.com/p/sources-ownport/downloads/list)
Исходный код доступен в репозиториии [http://code.google.com/p/sources-ownport/](http://code.google.com/p/sources-ownport/source/browse/rssfetch)
