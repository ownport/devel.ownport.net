Title: Описание процесса pyflowctrl
Author: d.rey
Date: 2012-01-25 07:57:00
Slug: pyflowctrl_25
Tags: process,description,pyflowctrl,metadata

Реализация процесса (класс Process) в pyflowctrl не позволяет определить с помощью [pymetainfo](http://code.google.com/p/pyflowctrl/source/browse/pyflowctrl/core4/processes/pymetainfo1.py) достаточную информацию о процессе, без дополнительного его описания. Получаемые метаданные избыточны и содержат специфичную для python внутреннюю реализацию. Пример вывода можно посмотреть в посте “[Исследования python-кода](http://devel.ownport.net/2012/01/c-python.html)”. 

После анализа процессов предыдущих релизов pyflowctrl был сформирован черновой список, необходимой информации о процессе:

- название процесса
- краткое описание процесса
- автор (-ы) кода процесса
- ссылки, где код процесса можно скачать
- зависимости с другими python модулями, библиотеками
- список потоков (входных, выходных, отладки)
- список функций с описанием

Если определять перечисленные выше свойства в конструкторе процесса, получение информации о них, будет возможно только после создании экземпляра класса Process. В связи с тем, что метаданные о процессе часто являются статичными переменными, их можно вынести как атрибут процесса

    :::python
    class Process(object):
        ''' Process '''
        
        __PROCESS__ = {
                        'name': 'Process',
                        'description': 'Basic process',
                        'author': '',
                        'url': '',
                        'depends on': '',
                        'io': {},
                    }
                    
Атрибут `__PROCESS__` содержит словарь метаданных процесса, на основании которого базовый конструктор процесса выполняет создание экземпляра процесса. Наиболее важным является ключ “io”, определяющий словарь параметров потоков ввода/вывода. Пример `__PROCESS__` на базе [pymetainfo2.py](http://code.google.com/p/pyflowctrl/source/browse/pyflowctrl/core4/processes/pymetainfo2.py):

    :::python
    __PROCESS__ = {
                        'name': 'PyMetaInfo',
                        'description': 'Extact metadata information about module/class. Customized for pyflowctrl.',
                        'author': 'Andrey Usov <http://devel.ownport.net>',
                        'url': 'http://pyflowctrl.googlecode.com/hg/pyflowctrl/core4/processes/pymetainfo2.py',
                        'io': {'input': {}, 'output': {}, },
                    }
Сейчас, если запустить pymetainfo2.py и указать в качестве аргумента его собственный код, вывод будет иметь следующий вид:

    :::
    $python examples/pymetainfo2.py pyflowctrl.core4.processes.pymetainfo2.PyMetaInfo
    
    {'__namespaces': {},
     'meta': {'based_on': (<class 'pyflowctrl.core4.Process'>,),
              'classes': [('__class__', <type 'type'>, <type 'object'>)],
              'data': [('__PROCESS__',
                        {'author': 'Andrey Usov <http://devel.ownport.net>',
                         'description': 'Extact metadata information about module/class. Customized for pyflowctrl.',
                         'io': {'input': {}, 'output': {}},
                         'name': 'PyMetaInfo',
                         'url': 'http://pyflowctrl.googlecode.com/hg/pyflowctrl/core4/processes/pymetainfo1.py'})],
              'description': 'Extact metadata information about module/class ',
              'funcs': [('__init__', None),
                        ('get_classes', 'return list of classes '),
                        ('get_data', 'return data for object. filtered'),
                        ('get_elements',
                         "get list of object's elements by type"),
                        ('get_funcs', 'return list of functions, filtered'),
                        ('get_metadata', "get dictionary of object's metadata "),
                        ('get_modpkgs', "return list of module's packages "),
                        ('main', None),
                        ('remove_empty_meta', 'remove empty data '),
                        ('run_once',
                         'wrapper for main loop \n\nif packet is specified run_once return the result of processing\nif packet is not specified run_once return the status of process')],
              'name': 'PyMetaInfo',
              'type': <type 'type'>},
     'module': 'pyflowctrl.core4.processes.pymetainfo2.PyMetaInfo'}
     
Следует отметить, что с переходом на новый способ определения потоков ввода/вывода, возвращен, определенный в core3, подход доступа к потокам ввода/вывода. 

получение пакета из входного потока _packet = process1.io['input'].get()_

запись пакета в поток вывода _process1.io['output'].put(packet)_

