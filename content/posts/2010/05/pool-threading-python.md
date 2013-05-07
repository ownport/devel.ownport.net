Title: Использование Pool для модуля threading (python)
Author: d.rey
Date: 2010-05-12 10:04:00
Slug: pool-threading-python
Tags: pool,map,python,parallel,threading

В стандартном модуле multiprocessing есть достаточно удобная возможность использования Pool (набора процессов). Задачи назначенные пулу будут выполняться параллельно. Количество процессов может быть выбрано произвольное, но часто встречается формула: число процессов равно числу ядер процессора умноженное на 2. Основная идея состоит в том, чтобы распределить выполнение задач между конечным числом процессов (число задач больше, чем число процессов).

К сожалению данная функциональность не включена в модуль threading. В сети можно найти уже готовые реализации (см. альтернативы). Не знаю почему, но я решил написать подобную функциональность самостоятельно. Тем более, что мне нужна только одна функциональность imap() - интерактивный map, позволяющий выполнить функцию для каждого элемента из набора входных данных и вернуть результат. В отличии от стандартного map() выполнение функции происходит параллельно.

Пример использования

    :::python
    import time
        import random
        from threads import Pool
        
        def f():
            t = random.randint(1,5)
            time.sleep(t)
            return 'sleep %d' % t
            
        p = Pool(3)
        it = p.imap(f, range(5))
        it.setTimeout(6)
        
        for d in it: print d

Создается пул из трех процессов. В него через метод imap() передается функция и набор из 5 случайных значений. Единственное, что делает функция, так это ожидает случайный интервала времени (от 1 до 5 секунд). Это достаточно интересный момент. В зависимости от входных данных, время выполнения функции может быть различно. Именно поэтому рекомендуется устанавливать значение таймера методом setTimeout - время, сколько ожидать результат выполнения функции.

Исходный код модуля [threads.py](https://code.google.com/p/sources-ownport/source/browse/parallel/libs/threads.py)

**Альтернативы**

- [Threadpool](http://www.chrisarndt.de/projects/threadpool/) 
- [Thread pool with same API as (multi)processing.Pool](http://code.activestate.com/recipes/576519-thread-pool-with-same-api-as-multiprocessingpool/) 

**Дополнительные ссылки**

- [multiprocessing.Pool.imap](http://docs.python.org/library/multiprocessing.html#multiprocessing.pool.multiprocessing.Pool.imap) 
- [intertools.imap](http://docs.python.org/library/itertools.html#itertools.imap)

