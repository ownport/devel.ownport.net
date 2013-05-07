Title: python: сравнение времени выполнения datetime.now() и time.time()
Author: d.rey
Date: 2011-06-15 11:22:00
Slug: python-datetimenow-timetime
Tags: datetime,compare,time,python

В исходной задаче необходимо получать разницу между вызовами функций. Для минимизации влияния нужно определить какая из функций выполняется быстрее. Кандидаты: datetime.now() и time.time(). 

    :::text
    $ python -m timeit -n 1000000 -s 'from datetime import datetime' 'datetime.now()'
    1000000 loops, best of 3: 4.3 usec per loop
    
    $ python -m timeit -n 1000000 -s 'import time' 'time.time()'
    1000000 loops, best of 3: 1.27 usec per loop
    
time.time() в выиграше

Время выполнения можно еще немного сократить, если использовать следующий хинт: 

    :::python
    from datetime import datetime
    import time
    
    dt = datetime.now
    tt = time.time

Новый результат: 

    :::text
    $ python -m timeit -n 1000000 -s 'from pytt import dt' 'dt()'
    1000000 loops, best of 3: 4.02 usec per loop
    python -m timeit -n 1000000 -s 'from pytt import tt' 'tt()'
    1000000 loops, best of 3: 0.937 usec per loop
