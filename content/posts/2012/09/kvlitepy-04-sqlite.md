Title: новая версия kvlite.py 0.4, добавлена поддержка sqlite
Author: d.rey
Date: 2012-09-17 10:01:00
Slug: kvlitepy-04-sqlite
Tags: collection,mysql,kvlite,sqlite,key-value,python,json,database

В новой версии [kvlite v0.4](https://github.com/ownport/kvlite), кроме [mysql](http://www.mysql.com/) добавлена поддержка [sqlite](http://www.sqlite.org/). В связи с этим изменена внутренняя архитектура библиотеки. Общие принципы работы с коллекциями [не изменились](http://devel.ownport.net/2012/06/kvlite2.html). Добавление поддержки sqlite позволяет работать с базами ключ/значение без необходимости установки mysql имея лишь стандартный пакет python. 

Дополнительно можно выбрать каким образом значения в коллекции будут сериализованы. В настоящий момент поддерживается два метода сериализации: cPickle (используется по-умолчанию) и Compressed JSON, но есть возможность добавление своиx методов через функцию open(). 

    :::python
    >>> collection = open('sqlite://memory:test_collection', serializer=CompressedJsonSerializer)

Сериализатор должен содержать методы dumps() для сериализации данных и loads() для обратной операции.

Пример использования:

    :::python
    >>> import kvlite
    >>> collection = open('sqlite://memory:test_collection')
    >>> collection
    <kvlite .sqlitecollection=".sqlitecollection" 0x14cb350="0x14cb350" at="at" object="object">
    </kvlite>
    
Форматы URI для функции open() 

- `mysql://username:password@hostname:port/database.collection_name`
- `sqlite://path-to-sqlite-file:collection_name`
- `sqlite://memory:collection_name`

в последнем случае, коллекция будет создана в памяти.

    :::python
    >>> collection.count
    0
    >>> collection.put('1', 'first document')
    >>> collection.put('2', ('second', 'document'))
    >>> collection.put('3', {'third': 'document'})
    >>> collection.count
    3
    >>> collection.get('1')
    ('1', 'first document')
    >>> collection.get('2')
    ('2', ('second', 'document'))
    >>> collection.get('3')
    ('3', {'third': 'document'})
    >>>
    >>> [k for k in collection.keys() ]
    ['1', '2', '3']
    >>> collection.delete('1')
    >>> collection.delete('2')
    >>> collection.delete('3')
    >>> collection.count
    0
    >>> collection.close()
    >>>
    
Для инсталляции достаточно скачать файл [kvlite.py](https://github.com/ownport/kvlite/raw/master/kvlite.py). Для работы с kvlite из консоли необходимо дополнительно скачать [kvlite-cli.py](https://github.com/ownport/kvlite/raw/master/kvlite-cli.py). 

