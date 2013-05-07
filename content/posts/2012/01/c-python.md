Title: Иcследование python кода
Author: d.rey
Date: 2012-01-12 08:20:00
Slug: c-python
Tags: pyflowctrl,python,code,introspection

[Интроспекция](http://ru.wikipedia.org/wiki/%D0%98%D0%BD%D1%82%D1%80%D0%BE%D1%81%D0%BF%D0%B5%D0%BA%D1%86%D0%B8%D1%8F_(%D0%BF%D1%80%D0%BE%D0%B3%D1%80%D0%B0%D0%BC%D0%BC%D0%B8%D1%80%D0%BE%D0%B2%D0%B0%D0%BD%D0%B8%D0%B5))(англ. type introspection) в программировании — возможность в некоторых объектно-ориентированных языках определить тип и структуру объекта во время выполнения программы. [Wikipedia](http://ru.wikipedia.org/). Для python эта возможность интегрирована в сам язык. Хороший пример интроспекции в python - встроенная система помощи. Для любого кода на python достаточно набрать в командной строке: 

    :::
    $ pydoc "module/class/function name"
    
как результат будет получено описание по модулю, классу или функции (см. примечание к посту ниже). 

Наибольший интерес представляет возможность анализа программы другой программой. В рамках проекта [pyflowctrl](http://code.google.com/p/pyflowctrl/) это может быть очень полезной функциональностью, когда менеджер процессов ProcessFlow получает информации о процессах и осуществляет соединение на основании характеристик входных/выходных потоков. 

Процесс [PyMetaInfo](http://pyflowctrl.googlecode.com/hg/pyflowctrl/core4/processes/pymetainfo1.py) позволяет провести анализ кода переданного имени модуля и вернуть метаданные в виде словаря. Пример использования процесса приведен в коде [examples/pymetainfo1.py](http://code.google.com/p/pyflowctrl/source/browse/pyflowctrl/core4/examples/pymetainfo1.py). На вход процесса передается пакет, содержащий имя модуля packet.module:

    :::python
    $ python examples/pymetainfo1.py pyflowctrl
    {'__namespaces': {},
     'meta': {'data': [('__author__', 'Andrey Usov <http: devel.ownport.net="">')],
              'description': 'Python Flow Control Library ',
              'module_packages': ['core4 (package)',
                                  'examples (package)',
                                  'libs (package)',
                                  'processes (package)',
                                  'streams (package)'],
              'name': 'pyflowctrl',
              'submodules': [('core4',
                              <module '="" 'pyflowctrl.core4'="" __init__.pyc'="" core4="" data="" devel="" from="" media="" pyflowctrl="">)],
              'type': <type 'module'="">},
     'module': 'pyflowctrl'}
     
На выходе получаем пакет с метаданными о библиотете. Пример вывода информации о процессе:  

    :::
    $ python examples/pymetainfo1.py pyflowctrl.core4.processes.pymetainfo1.PyMetaInfo
    {'__namespaces': {},
     'meta': {'based_on': (<class 'pyflowctrl.core4.process'="">,),
              'classes': [('__class__', <type 'type'="">, <type 'object'="">)],
              'data': [('__dict__',
                        dict_proxy({'__module__': 'pyflowctrl.core4.processes.pymetainfo1', 'get_elements': <function 0x2a21b18="" at="" get_elements="">, 'get_classes': <function 0x2a21b90="" at="" get_classes="">, 'get_modpkgs': <function 0x2a21c08="" at="" get_modpkgs="">, 'get_metadata': <function 0x2a21c80="" at="" get_metadata="">, 'main': <function 0x2a21cf8="" at="" main="">, '__doc__': ' Extact metadata information about module/class ', '__init__': <function 0x29ba938="" __init__="" at="">})),
                       ('__weakref__',
                        <attribute '__weakref__'="" 'process'="" objects="" of="">)],
              'description': 'Extact metadata information about module/class ',
              'funcs': [('__delattr__',
                         <slot '__delattr__'="" 'object'="" objects="" of="" wrapper="">),
          
                        . . .
    
                        ('get_classes', <unbound method="" pymetainfo.get_classes="">),
                        ('get_elements',
                         <unbound method="" pymetainfo.get_elements="">),
                        ('get_metadata',
                         <unbound method="" pymetainfo.get_metadata="">),
                        ('get_modpkgs', <unbound method="" pymetainfo.get_modpkgs="">),
                        ('main', <unbound method="" pymetainfo.main="">),
                        ('run_once', <unbound method="" pymetainfo.run_once="">)],
              'name': 'PyMetaInfo',
              'type': <type 'type'="">},
     'module': 'pyflowctrl.core4.processes.pymetainfo1.PyMetaInfo'}
     
Информация в выводе достаточно избыточна для работы pyflowctrl, отсутствует информация о потоках. Так как потоки создаются в `__init__`(), провести анализ содержимого объекта без его создания не получится. Необходимо пересмотреть принципы создания процессов для того, что бы существовала возможность получения информации о потоках без создания самого процесса.

**Примечание**: 

документация в python формируется автоматически на основании анализа кода объекта. При вызове pydoc выполняет импорт кода и проводит анализ его структуры, извлекает документацию.
