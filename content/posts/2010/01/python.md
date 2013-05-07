Title: Декораторы в python
Author: d.rey
Date: 2010-01-03 11:35:00
Slug: python
Tags: decorator,debug,python

[Understanding decorators](http://uswaretech.com/blog/2009/06/understanding-decorators/), на мой взгляд лучшая статья описывающая принципы работы декораторов в Python. Достаточно просто и понятно используется описание декораторов на основе объектов, а не на базе функций, как чаще всего можно встретить в интернете.

В качестве примера рассмотрим один из способов отладки кода на python. Достаточно часто необходимо в процессе разработки выяснить, какие параметры были переданы функции или методу. Задача достаточна общая и применима практически ко всем функциям/методам.


Код декоратора:

    :::python
    class ddebug(object):
        ''' Debug decorator for function/method '''
        def __init__(self, orig_func):
            self.orig_func = orig_func
            print 'ddebug.__init__(orig_func): %s' % orig_func.__name__
    
        def __call__(self, *args, **kargs):
            print 'ddebug.__call__(args): %s' % str(args)
            print 'ddebug.__call__(kargs): %s' % str(kargs)
            return self.orig_func(*args, **kargs)

При вызове декоратора, методу `__init__` в качестве параметра передается функция, которая определена непосредственно после декоратора. Параметры функции передаются через метод `__call__`.

В качестве примера применим ddebug для функции test_func:

    :::python
    @ddebug
    def test_func(a,b, c='value_c'):
        print a, b, c

Вызываем функцию test_func(1,2,c='test'):

    :::python
    ddebug.__init__(orig_func): test_func
    ddebug.__call__(args): (1, 2)
    ddebug.__call__(kargs): {'c': 'value_c'}
    1 2 value_c

Первые три строки результат работы декоратора ddebug, последняя строка - результат работы функции test_func.

**Дополнительная информация о декораторах**

- PEP318, Decorators for functions and methods [http://www.python.org/dev/peps/pep-0318/](http://www.python.org/dev/peps/pep-0318/)
- Wikipedia: Синтаксис и семантика Python - декоратор [http://en.wikipedia.org/wiki/Python_syntax_and_semantics#Decorators](http://en.wikipedia.org/wiki/Python_syntax_and_semantics#Decorators)
- Python Decorator Library [http://wiki.python.org/moin/PythonDecoratorLibrary](http://wiki.python.org/moin/PythonDecoratorLibrary)
- Python 2.4 Decorators. Reducing code duplication and consolidating knowledge. [http://www.ddj.com/web-development/184406073](http://www.ddj.com/web-development/184406073)
