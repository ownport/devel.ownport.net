Title: Проверка типа (тесты производительности) на python
Author: d.rey
Date: 2010-03-08 10:17:00
Slug: python
Tags: performance,python,test

Воодушевленный [презентацией](http://pycamp.org.ua/presentation/6) [Александра Шигина](http://friendfeed.com/shigin) на проходившей в январе [PyCamp](http://pycamp.org.ua/) пообещал себе, что обязательно применю методы проверки производительности, предложенные Александром. Случай не заставил себя долго ждать. Возникла задача обработки достаточно большого количества сырых данных перед прогрузкой их в базу данных. Одной из часто выполняемых операций в данной задаче является проверка типа переменной. 


Способы, которыми можно выполнить проверку несколько:

_встроенными методами:_

    :::python
    * if type(<var>).__name__=='<var_type>'
        * if type(<var>)==type(int())
        
_с помощью модуля types:_

    :::python
    * if type(<var>) is types.<Var>Type
        * if type(<var>) == types.<Var>Type

Скрипт для тестирования доступен на [source-ownport](http://code.google.com/p/sources-ownport/source/browse/performance_tests/check_types.py). Запускается достаточно просто: _python check_types.py_. Основная задача скрипта, - это выполнение проверки типа разными способами по 10 млн. раз для каждого варианта проверки.

Результат выполнения скрипта имеет следующий вид:

    :::
    if type(<var>).__name__=='<var_type>'
    1.1. Time elapsed when <var> is integer: 12.7684593992 seconds
    1.2. Time elapsed when <var> is not integer: 12.22267886 seconds
    
    via types module: if type(i) is types.IntType
    2.1. Time elapsed when <var> is integer: 9.16543136069 seconds
    2.2. Time elapsed when <var> is not integer: 9.45736427395 seconds
    
    via types module: if type(i) == types.IntType
    3.1. Time elapsed when <var> is integer: 9.45031561274 seconds
    3.2. Time elapsed when <var> is not integer: 9.47753220032 seconds
    
    <b>via types module: if type(i) is IntType
    4.1. Time elapsed when <var> is integer: 7.87745900666 seconds
    4.2. Time elapsed when <var> is not integer: 7.86522644638 seconds
    
    via types module: if type(i) == IntType
    5.1. Time elapsed when <var> is integer: 8.12291590132 seconds
    5.2. Time elapsed when <var> is not integer: 8.27180465674 seconds
    </b>
    if type(<var>)==type(int())
    6.1. Time elapsed when <var> is integer: 18.5928440626 seconds
    6.2. Time elapsed when <var> is not integer: 18.4365007284 seconds

Наилучший показатель получился при использовании модуля types (типы экспортированы заранее, _from types export IntType, StringType_): **if type(i) is IntType**. На что стоит обратить внимание, что при использовании оператора сравнения "==" результат несколько хуже, чем при использовании "is", разница 0.25 секунды

Вывод: самые простые операции могут сохранить время при знании их специфики и правильном использовании.

Примечание: тесты проводились на python версии 2.6.3
