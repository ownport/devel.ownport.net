Title: Расчет контрольных сумм файлов
Author: d.rey
Date: 2011-07-23 09:31:00
Slug: blog-post_23
Tags: sha1,file,hash,pyflowctrl,sha512,python,md5,sha256,sha224,sha384

Контрольные суммы файлов используются для проверки целостности файлов, сравнения файлов на неэквивалентность. Расчет контрольных сумм выполняется на основе специальных хеш-функций, на вход которых передают исходные данные произвольной длины, на выходе получают битовую строку фиксированной длины - хеш-кодом, соответствующей исходным данным. В общем случае однозначного соответствия между исходными данными и хеш-кодом нет в силу того, что количество значений хеш-функций меньше, чем вариантов входного массива.

Существует множество исходных данных, которым соответствуют одинаковые хеш-коды. Такие ситуации называются коллизиями. В зависимости от поставленной задачи необходимо внимательно подходить к выбору функций. Процесс [FileHash](http://code.google.com/p/sources-ownport/source/browse/pyflowctrl/processes/filehash1.py) позволяет делать расчет хеш-кодов для файлов используя хеш-функции, включенные в стандартную поставку python: md5, sha1, sha224, sha256, sha384, sha512.

Пример использования процесса FileHash:

    :::python
    from core2 import EmptyStream, ProcessFlow
    from processes.treewalker1 import TreeWalker
    from processes.filehash1 import FileHash
    from processes.stdoutprinter1 import Printer
    
    flow = ProcessFlow()
    id_walker = flow.new(TreeWalker())
    id_hash = flow.new(FileHash(algorithm='md5'))
    id_print = flow.new(Printer())
    
    flow.link({'sid': id_walker, 'son': 'output', 'tid': id_hash, 'tin': 'input'})
    flow.link({'sid': id_hash, 'son': 'output', 'tid': id_print, 'tin': 'input'})
    
    flow.pmap[id_walker].io['input'].put('/media/photos/')
    flow.run()
    
Приведенный выше пример очень похож на пример из предыдущего поста “[Работа с текстовыми файлами небольшого объема](http://devel.ownport.net/2011/07/blog-post_22.html)”. С небольшим отличием - вместо FileReader используется процесс [FileHash](http://code.google.com/p/sources-ownport/source/browse/pyflowctrl/processes/filehash1.py). Выбор алгоритма хеш-функции осуществляется с помощью algorithm, который может принимать значения md5, sha1, sha224, sha256, sha384, sha512. Результатов выполнения данного скрипта будет вывод на консоль пар значений для файлов из исходной директории: путь к файлу и его контрольная сумма.

**Полезные ссылки**:

Cryptographic hash function, [http://en.wikipedia.org/wiki/Cryptographic_hash_function](http://en.wikipedia.org/wiki/Cryptographic_hash_function)

