Title: Автоматическое добавление алиасов в профайлер
Author: d.rey
Date: 2011-06-18 05:56:00
Slug: blog-post_18
Tags: aliases,profiler,pyflowctrl,python

В [предыдущем посте](http://devel.ownport.net/2011/06/blog-post.html) упоминалось о возможности добавления алиасов - соотвествие имени файла и номера строки кода, методу объекта. Для этого в Profiler.code_aliases передавался словарь алиасов. Данный словарь нужно было описывать вручную. Для упрощения данной задачи в профайлер добавлены два метода: get_aliases() и update_aliases()

`Profiler.get_aliases(module_name)`

возвращает словарь алиасов для указанного модуля. Метод статический, т.е. можно вызывать без инициализация 

    :::python
    >>> from profiler import Profiler
    >>> Profiler.get_aliases('core2')
    {'core2.py:50': 'core2.Process.run_once()', 'core2.py:56': 'core2.ProcessFlow.__init__()',
    'core2.py:45': 'core2.Process.main()', 'core2.py:61': 'core2.ProcessFlow.new()', 
    'core2.py:41': 'core2.Process.__init__()', 'core2.py:66': 'core2.ProcessFlow.link()',
    'core2.py:28': 'core2.Stream.get()', 'core2.py:89': 'core2.ProcessFlow.run()', 
    'core2.py:13': 'core2.Stream.__init__()', 'core2.py:36': 'core2.Stream.put()'}

`Profiler.update_aliases(aliases)`

обновляет словарь алиасов для объекта Profiler 

    :::python
    p = Profiler()
    p.update_aliases(p.get_aliases('core2'))
    p.update_aliases(p.get_aliases('profiler'))
    p.update_aliases(p.get_aliases('core2test4'))
    p.activate()
    <your code>
    p.deactivate()
    p.print_stats()
    
Результат 

    :::text
    $ time python /devel/pyflowctrl/examples/example5.py > /dev/null
     ncalls    percall    tottime filename:lineno
          3   0.000007   0.000022 core2.Stream.__init__()
        1001   0.000006   0.005707 core2.Stream.get()
        1000   0.000005   0.005123 core2.Stream.put()
          2   0.000010   0.000021 core2.Process.__init__()
          4   0.005722   0.022889 core2.Process.run_once()
          1   0.000009   0.000009 core2.ProcessFlow.__init__()
          2   0.000004   0.000008 core2.ProcessFlow.new()
          1   0.000025   0.000025 core2.ProcessFlow.link()
          1   0.022938   0.022938 core2.ProcessFlow.run()
          1   0.000048   0.000048 core2test4.IntGeneratorProcess.__init__()
          2   0.005206   0.010411 core2test4.IntGeneratorProcess.main()
          1   0.000034   0.000034 core2test4.PrinterProcess.__init__()
          1   0.012434   0.012434 core2test4.PrinterProcess.main()
          1   0.000000   0.000000 profiler.Profiler.activate()
          1   0.000000   0.000000 profiler.Profiler.deactivate()

Обновленный profiler и пример использования examples/example5.py в репозитории [pyflowctrl](http://code.google.com/p/sources-ownport/source/browse/pyflowctrl/)
