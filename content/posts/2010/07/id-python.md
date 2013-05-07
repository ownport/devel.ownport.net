Title: Способы генерации уникальных ID в python
Author: d.rey
Date: 2010-07-25 09:26:00
Slug: id-python
Tags: unique,python,identity,generator

Разбираясь с [freebase](http://www.freebase.com/), а в частности с генерацией [machine ID](http://wiki.freebase.com/wiki/Mid). Задался вопросом: какие методы генерации уникальный ID существуют в python. Данный пост является кратким обзором методов.

**Метод 1**

Наверное самый простой путь - это использование модуля uuid из стандартной библиотеки python. Модуль предоставляет возможность генерации уникальных ID четырьмя методами, которые описаны в [RFC4122](http://tools.ietf.org/html/rfc4122.html)

uuid1 позволяет генерировать уникальные ID состоящие из host ID, последовательного числа и текущего времени.

    :::python
    >>> uuid.uuid1()
    UUID('0c6eab40-97ad-11df-b10d-00215c5600a3')

uuid3 и uuid5 в некотором смысле похожи, так как используют в качестве аргументов namespace и name. Тольк в uuid3 используется MD5 алгоритм, а uuid5 SHA-1

    :::python
    >>> uuid.uuid3(uuid.NAMESPACE_DNS, 'ownport.net')
    UUID('66b1a6a0-4290-31bc-9593-dd600cdbde84')
    >>> uuid.uuid5(uuid.NAMESPACE_DNS, 'ownport.net')
    UUID('d7ea238f-eed2-5150-abc0-97a56e9da78c')

для случая, если нужно сгенерировать случайный ID, используется uuid4

    :::python
    >>> uuid.uuid4()
    UUID('d1c2aab0-d92e-4bdb-8bc9-bbeb7a1f686a')

**Метод 2**

использование функции os.urandom(n), возвращающей строку из n случайных байт, позволяет генерировать случайные последовательности

    :::python
    >>> print ''.join([hex(ord(c)).replace('0x','') for c in os.urandom(8)])
    'a0103cfe4991cf98'

в приведенном примере для генерации ID используется случайная 8 байтовая последовательность.

**Метод 3**

В [данном методе](http://code.activestate.com/recipes/65110-generate-unique-session-ids/) для генерации уникального ID используется расчет функции MD5 от двух timestamp-ов с добавлением префикса.

    :::python
    import os
    import time
    import random
    import hashlib
    
    def getNewSID(tag):
        ''' Build a new session ID '''
        t1 = time.time()
        time.sleep(random.random())
        t2 = time.time()
        base = hashlib.md5(tag + str(t1+t2)).hexdigest()
        return '_'.join((tag, base))
    
    >>> print getNewSID('ownport')
    'ownport_a1c0a8213096c7896982504d64dbd7a7'

Примечание: исходный код был несколько адаптирован для работы с python 2.7.

