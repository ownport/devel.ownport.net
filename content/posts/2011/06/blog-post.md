Title: Профайлер своими руками, так ли это сложно?
Author: d.rey
Date: 2011-06-17 07:27:00
Slug: blog-post
Tags: debug,profiler,python

Профайлеры используются для сбора характеристик работы программы, такие как время выполнения определенных участков кода, количество вызовов функций и т.д. Для этих целей в базовую библиотеку python входит profile и cProfile. Первый, полностью написан на python, второй на C. cProfile рекомендуется как наиболее быстрый. Для случаев, когда нужно добавить дополнительный функционал в работу профайлера нужно использовать profile. Именно благодаря ему и появилась идея написания своего небольшого профайлера. Такая ли это сложная задача?

Если взглянуть на код profile.py из стандартной библиотеки, видно, что основную роль в перехвате вызовов функций осуществляет сам python. Активируется перехват событий функцией [sys.setprofile(profilefunc)](http://docs.python.org/library/sys.html#sys.setprofile). Функция profilefunc вызывается каждый раз когда происходит вызов или возврат из функции. В качестве аргументов передаются три параметра: frame, event, arg. Подробное описание значений можно найти в [документации](http://docs.python.org/library/sys.html#sys.settrace).

Использование функции setprofile() позволяет активировать профайлер как для все кода, так и для некоторого его участка. В качестве простого примера воспользуемся [profiler.py](http://code.google.com/p/sources-ownport/source/browse/pyflowctrl/profiler.py) и примером [examples/example4.py](http://code.google.com/p/sources-ownport/source/browse/pyflowctrl/examples/example4.py) из фреймфорка [pyflowctrl](http://code.google.com/p/sources-ownport/source/browse/pyflowctrl/). Разбирать сам код не имеет большого смысла, поэтому остановимся более подробно на его использовании. В общем, работу с profiler.py можно представить в виде: 

    :::python
    from profiler import Profiler
       
    p = Profiler()
    p.activate()
    
    <your code>
    
    p.deactivate()
    p.print_stats()
    
После вызова p.activate() профайлер выполняет перехват 3 событий: call, return и exception. На основании перехваченных данных собирается информация о количестве вызовов функций, времени их выполнения. Вывод результатов print_stats() осуществлется в стандартный поток ошибок STDERR. 

    :::text
    $ time python examples/example4.py > /dev/null
     ncalls    percall    tottime filename:lineno
          3   0.000010   0.000029 /devel/pyflowctrl/core2.py:13
       1001   0.000005   0.005332 /devel/pyflowctrl/core2.py:28
       1000   0.000005   0.005062 /devel/pyflowctrl/core2.py:36
          2   0.000008   0.000017 /devel/pyflowctrl/core2.py:41
          4   0.005660   0.022642 /devel/pyflowctrl/core2.py:50
          1   0.000009   0.000009 /devel/pyflowctrl/core2.py:56
          2   0.000005   0.000010 /devel/pyflowctrl/core2.py:61
          1   0.000032   0.000032 /devel/pyflowctrl/core2.py:66
          1   0.022701   0.022701 /devel/pyflowctrl/core2.py:89
          1   0.000000   0.000000 /devel/pyflowctrl/profiler.py:56
          1   0.000000   0.000000 /devel/pyflowctrl/profiler.py:60
          1   0.000045   0.000045 core2.test3.py:11
          2   0.005288   0.010575 core2.test3.py:17
          1   0.000037   0.000037 core2.test3.py:23
          1   0.012028   0.012028 core2.test3.py:29
    
    real    0m0.097s
    user    0m0.070s
    sys    0m0.020s
    
Отображение ссылок на функции в виде имя файла и номер строки не удобен. Для улучшения читабельности результатов можно создать словарь подстановок и передать его в профайлер: 

    :::python
    p.code_aliases = {
           '/devel/pyflowctrl/core2.py:13': 'Stream.__init__()',
           '/devel/pyflowctrl/core2.py:28': 'Stream.get()',
           '/devel/pyflowctrl/core2.py:36': 'Stream.put()',
           '/devel/pyflowctrl/core2.py:41': 'Process.__init__()',
           '/devel/pyflowctrl/core2.py:50': 'Process.run_once()',
           '/devel/pyflowctrl/core2.py:56': 'ProcessFlow.__init__()',
           '/devel/pyflowctrl/core2.py:61': 'ProcessFlow.new()',
           '/devel/pyflowctrl/core2.py:66': 'ProcessFlow.link()',
           '/devel/pyflowctrl/core2.py:89': 'ProcessFlow.run()',
    }
Теперь результаты имеют более осмысленный вид 

    :::text
    $ time python examples/example4.py > /dev/null
     ncalls    percall    tottime filename:lineno
          3   0.000007   0.000022 Stream.__init__()
       1001   0.000005   0.005423 Stream.get()
       1000   0.000005   0.005087 Stream.put()
          2   0.000008   0.000017 Process.__init__()
          4   0.005685   0.022741 Process.run_once()
          1   0.000009   0.000009 ProcessFlow.__init__()
          2   0.000004   0.000008 ProcessFlow.new()
          1   0.000024   0.000024 ProcessFlow.link()
          1   0.022794   0.022794 ProcessFlow.run()
          1   0.000000   0.000000 /media/d2/devel/pyflowctrl/profiler.py:56
          1   0.000000   0.000000 /media/d2/devel/pyflowctrl/profiler.py:60
          1   0.000042   0.000042 core2.test3.py:11
          2   0.005294   0.010588 core2.test3.py:17
          1   0.000033   0.000033 core2.test3.py:23
          1   0.012114   0.012114 core2.test3.py:29
    
    real    0m0.095s
    user    0m0.080s
    sys    0m0.010s

Если вернуться к вопросу из темы, так ли это сложно? Думаю, что пока можно ответить, что не очень :)

