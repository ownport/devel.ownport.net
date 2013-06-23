Title: Новая версия kvlite 0.5
Author: d.rey
Date: 2013-06-14 09:25:00
Slug: kvlite-0.5
Tags: collection, mysql, kvlite, sqlite, key-value, python, json, database

Новая версия kvlite 0.5. доступна на [pypi](https://pypi.python.org/pypi/kvlite) и [github](https://github.com/ownport/kvlite) в master ветви. Из изменений:

Добавлена возможность исследования структуры документов, хранящихся в базе данных kvlite. Функции
_dict2flat()_ and _doc_struct()_. В kvlite-cli их работу можно увидеть с помощь команды _scheme_

Возможность получения документов по-странично при запросе всех документов в базе

    :::python
    PAGE_SIZE=100
    for (k, v) in collection.get(offset=0,limit=PAGE_SIZE):
        print k, v

либо получение документов для определенной страницы N

    :::python
    PAGE_SIZE=100
    for (k, v) in collection.get(offset=N*PAGE_SIZE,limit=PAGE_SIZE):
        print k, v

Единый доступ к получению документов, метод collection.get()

сollection.get() может возвращать документы по нескольким ключам

Удален метод collection.keys()

kvlite-cli.py поддерживает функциональность kvlite v0.5

Обновлена [kvlite диаграмма](https://raw.github.com/ownport/kvlite/master/docs/kvlite.png)

Обновлена [документация](https://github.com/ownport/kvlite/blob/master/README.rst) 
