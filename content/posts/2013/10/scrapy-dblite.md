Title: scrapy-dblite, библиотека для хранения данных Scrapy в sqlite
Author: d.rey
Date: 2013-10-08 08:14:00
Slug: scrapy-dblite-first-release
Tags: python, scrapy, sqlite, database

На выходных закончил работу над небольшой библиотекой для [Scrapy](http://scrapy.org/), позволяющей упростить хранение данных (Scrapy Items) в sqlite базе данных без необходимости работы с базой на уровне SQL запросов. 

В качестве примера работы с библиотекой, рассмотрю простой пример, когда необходимо в базе данных хранить данные по неким продуктам, извлеченных с помощью Scrapy: 

	:::python
	from scrapy.item import Item, Field

    class Product(Item):
        _id         = Field()
        name        = Field()
        price       = Field() 
        catalog_url	= Field(dblite="text unique")

Так обычно выглядит обычный Scrapy Item класс. Создадим на базе этого класса несколько элементов

    :::python
    >>> p1 = Product(name='Laptop', price=1000, catalog_url="http://catalog/id/1") 
    >>> p2 = Product(name='Nettop', price=100, catalog_url="http://catalog/id/2")
    >>> p3 = Product(name='Desktop PC', price=500, catalog_url="http://catalog/id/3")
    >>> p1, p2, p3
    ({'name': 'Laptop', 'price': 1000, catalog_url="http://catalog/id/1"}, 
    {'name': 'Nettop', 'price': 100, catalog_url="http://catalog/id/2"}, 
    {'name': 'Desktop PC', 'price': 500, catalog_url="http://catalog/id/3"})

Создадим sqlite базу данных 

    :::python
    >>> import dblite
    >>> ds = dblite.open(Product, 'sqlite://product.sqlite:items')
    >>> ds
    <dblite.Storage object at 0x7f72edcff490>

В качестве параметров в функцию dblite.open() передаются два параметра. Это `Product` - класс на основании которого будет создана таблица sqlite, если она не была созданна ранее и `sqlite://product.sqlite:items` - [URI](http://en.wikipedia.org/wiki/Uniform_resource_identifier) с информацией, в какой базе данных и в какой таблице будут храниться данные. Формат URI: sqlite://database:table. 

В функцию dblite.open() можно также передать параметр `autocommit`, который может принимать следующие значения:

- False - значение по умолчанию. Режим ручного commit'a данных
- True - используется, когда необходимо выполнить commit после каждой вставки данных
- числовое значение - определяет через сколько вставок данных необходимо выполнить commit()

Работа с данными в dblite релизована через три метода: get(), put(), delete()

    :::python
    >>> p1, p2, p3
    ({'name': 'Laptop', 'price': 1000, catalog_url="http://catalog/id/1"}, 
    {'name': 'Nettop', 'price': 100, catalog_url="http://catalog/id/2"}, 
    {'name': 'Desktop PC', 'price': 500, catalog_url="http://catalog/id/3"})
    >>> ds.put(p1)
    >>> ds.put([p2,p3])
    >>> len(ds)
    3
    >>> [p for p in ds.get()]
    [{'_id': 1, 'catalog_url': u'http://catalog/id/1', 'name': u'Laptop', 'price': 1000}, 
    {'_id': 2, 'catalog_url': u'http://catalog/id/2', 'name': u'Nettop', 'price': 100}, 
    {'_id': 3, 'catalog_url': u'http://catalog/id/3', 'name': u'Desktop PC', 'price': 500}]
    >>> for p in ds.get():
    ...     ds.delete(p)
    ...
    >>> len(ds)
    3
    >>>

Более детальное описание библиотеки dblite c примерами доступно на [github/scrapy-dblite](https://github.com/ownport/scrapy-dblite)



