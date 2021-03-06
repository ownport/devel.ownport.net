Title: Многопотоковый URLFetcher
Author: d.rey
Date: 2010-04-21 10:40:00
Slug: urlfetcher
Tags: url,thread,fetch,python,parallel

Последнее время все чаще возникают вопросы о разработке приложений для многопроцессорных систем. Рост тактовой частоты процессоров больше не возможен, выбор сделан в сторону увеличения числа ядер. Но готовы ли мы, писать приложения с использованием многопоточности? Этот вопрос я задал себе. Познакомившись с нескольким теоретическими статьями на wikipedia я решил попробовать переписать ряд своих наработок с использованием нового для себя подхода. В теории казалось все так просто. Но на практике, разработка многопотокового приложения соизмерима с управлением городским трафиком. Даже несколько потоков, работающие каждый в своем режиме могут создавать сложности. Наиболее сложными для меня (и в то же время невероятно интересными) были вопросы синхрозации данных между потоками. Если однопотоковое приложение можно сразу сесть и написать и оно будет работать, то для простейшего многопотокового нужно хорошенько все продумать. Для случая с одним элементом для которого распараллеливаются задачи - все более менее понятно. все самое интересное начинается как только таких элементов появляется несколько и они зависимы между собой. 

Для начинающего самым правильным является путь разработки от простого к сложному. Пройдясь по свежим следам, я решил переписать скрипт URLFetcher, который я описал в посте ["UrlFetcher или как собирать данные в Интернет"](http://devel.ownport.net/2010/03/urlfetcher.html). 

Для разработки любого приложения важно иметь спецификацию, для многопотокового приложения - это обязательное условие. Диаграмма взаимодействия элементов URLFetcher:

![](http://4.bp.blogspot.com/_XzhxWqanLlk/S86q3goJXkI/AAAAAAAAAH4/DNybpqaTYMw/s320/parallel_urlfetcher.png)

Как видно из диаграммы, как исходные так и результирующие данные хранятся в общей базе данных. В данном случае используется sqlite3. Информация о веб странице включает в себя: url: ссылку, type: тип страницы (html, rss), content: содержимое, datetime: дата и время последнего обновления. При запуске скрипта список URL извлекается из базы данных и записывается в Queue (urls for fetching). Потоки URLFetch на основании этих URL, загружают страницы, а результат сохраняют в следующем Queue (results of fetching). Скрип завершает свое выполнение, как только проверены все URL из первого Queue.

Для оценки достоинства многопотокового URLFetcher я провел небольшой эксперимент. Исходные данные: 250 ссылок на 250 различных источников (RSS ленты, подкасты, веб сайты). Проверка одним потоком заняла около 12 минут, 10-ю потоками - 2 минуты, 20-ю: минута 50 секунд. Достоинства многопоточности для решения задач подобного класса очевидны. Правда важен компромисс между количеством потоков и эффективностью. Как видно из эксперимента, увеличение до 20 потоков дало лишь уменьшение времени выполнения на 10 секунд по сравнению с 10 потоками.

Скрипт urlfetcher написан на python (как и его [однопотоковый предшественник](http://devel.ownport.net/2010/03/urlfetcher.html)) и доступен в [репозитории](http://code.google.com/p/sources-ownport/source/browse/#hg/parallel). У него есть несколько зависимостей с небольшими модулям libs/debug.py, libs/urldb.py, workers/urlfetch.py. Их также можно найти в репозитории.

Интерфейс работы достаточно прост: для добавления URL в базу необходимо воспользоваться небольшим менеджером который входит в состав модуля urldb.py.

    :::sh
    Usage: urldb.py [options]
    
    Options:
      -h, --help            show this help message and exit
      -d DATABASE, --database=DATABASE
                            URL database, by default: urldb.sqlite
      -a ADD_URL, --add_url=ADD_URL
                            add URL to database
      -l, --url_list        printout URL's list in database

Синтаксис для добавления: 

    :::sh
    python urldb.py -d urldb.sqlite -a http://devel.ownport.net

После того как URL добавлены, можно выполнить закачку страниц с помощью urlfetcher.py. 

    :::sh
    Usage: urlfetcher.py [options]
    
    Options:
      -h, --help            show this help message and exit
      -d DATABASE, --database=DATABASE
                            URL database, by default: urls.sqlite
      -t THREADS, --threads=THREADS
                            threads for URL checking

Синтаксис: 

    :::sh
    python urlfetcher.py -d urldb.sqlite -t 10

Аргумент -t определяет количество потоков для загрузки страниц.

В заключении добавлю несколько слов о потоках и процессах. Для скриптов написаны на python реализация потоков (thread) имеет свою специфику. Потоки не увеличивают производительность, даже если у вас несколько ядер. Потоки создаются внутри одного ядра и их количество ограничено. В данном примере потоки позволяют уменьшить время выполнения скрипта только благодаря неоднородности запрос/ответ со стороны удаленных веб серверов. Время ответов для каждого сайта различно и это было использовано: пока один поток ждет ответа, другие выполняют свои работы. Мощности процессора, даже одного ядра вполне достаточного для того, чтобы обрабатывать поступающий поток данных. 

Если необходимо выполнить вычисления ориентированные на работу процессора рекомендую рассмотреть модуль multiprocessing, который входит в стандартный пакет python начиная с версии 2.6. Я постараюсь рассмотреть этот модуль более детально в ближайших своих постах, т.к. работа с параллельным вычислениями меня очень заинтересовала. Да и есть реальные задачи.

