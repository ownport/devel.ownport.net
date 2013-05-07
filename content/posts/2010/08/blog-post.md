Title: Структура баз данных ключ/значение
Author: d.rey
Date: 2010-08-16 06:08:00
Slug: blog-post
Tags: structure,key-value,python,nosql,database

Для реляционных баз данных определить структуру таблицы не составляет большого труда, так как количество и типы полей строго определены. Для [баз данных ключ/значение](http://devel.ownport.net/2010/08/friendfeed.html) значения представляют собой сериализированные объекты. Если поле не определено в настоящий момент, оно попросту не заносится в базу данных. Благодаря этому, число полей для каждого объекта может отличаться. Примером могут служить статьи из RSS лент: для одних статей автор один, для других авторов может быть несколько. Может отличаться число тегов, не быть краткого описания статьи и многое другое. Гибкость структуры приводит к появлению сложностей, с которыми необходимо научиться работать.


Для лучшего понимания работы со структурой объекта, можно взять за основу пример из прошлого поста [“Создание коллекции видео материалов GoogleDevelopers”](http://devel.ownport.net/2010/08/googledevelopers.html). Предположим, что данные уже собраны и хранятся в базе данных, но о структуре хранения объектов данных ничего не известно. 

Если просто извлечь данные их базы, становится понятно, что основная сложность анализа данных состоит в том, что, во-первых, структура данных имеет древовидную форму, а во-вторых, данные и структура смешаны. 

Провести анализ структуры данных можно с помощью небольшой библиотеки datastore_utils.py, доступной на [sources-ownport](http://sources-ownport.googlecode.com/hg/parallel/libs/datastore_utils.py). Библиотека содержит две функции struct и structs.

Функция `struct(entity)` выделяет структуру из данных объекта. Пример структуры данных для GoogleDevelopers имеет слудующий вид:

Код:

    :::python
    ds = Datastore('mysql://<username>:<password>@localhost/<database>')
    pprint(struct(ds.get('google_developers')[0]))

Вывод:

    :::python
    ["<type 'long'>",
     "<type 'str'>",
     {'author': "<type 'unicode'>",
      'author_detail': {'name': "<type 'unicode'>"},
      'id': {'gr:original-id': "<type 'unicode'>",
             'original-id': "<type 'unicode'>"},
      'link': "<type 'unicode'>",
      'links': [{'href': "<type 'unicode'>",
                 'rel': "<type 'unicode'>",
                 'type': "<type 'unicode'>"}],
      'published': "<type 'unicode'>",
      'published_parsed': "<type 'time.struct_time'>",
      'source': {'id': "<type 'unicode'>",
                 'link': "<type 'unicode'>",
                 'links': [{'href': "<type 'unicode'>",
                            'rel': "<type 'unicode'>",
                            'type': "<type 'unicode'>"}],
                 'title': "<type 'unicode'>",
                 'title_detail': {'base': "<type 'unicode'>",
                                  'language': "<type 'NoneType'>",
                                  'type': "<type 'str'>",
                                  'value': "<type 'unicode'>"}},
      'summary': "<type 'unicode'>",
      'summary_detail': {'base': "<type 'unicode'>",
                         'language': "<type 'NoneType'>",
                         'type': "<type 'str'>",
                         'value': "<type 'unicode'>"},
      'tags': [{'label': "<type 'unicode'>",
                'scheme': "<type 'unicode'>",
                'term': "<type 'unicode'>"},
               {'label': "<type 'unicode'>",
                'scheme': "<type 'unicode'>",
                'term': "<type 'unicode'>"},
               {'label': "<type 'NoneType'>",
                'scheme': "<type 'unicode'>",
                'term': "<type 'unicode'>"}],
      'title': "<type 'unicode'>",
      'title_detail': {'base': "<type 'unicode'>",
                       'language': "<type 'NoneType'>",
                       'type': "<type 'str'>",
                       'value': "<type 'unicode'>"},
      'updated': "<type 'unicode'>",
      'updated_parsed': "<type 'time.struct_time'>"}]

Видно, что стоит добавить данные вместо описания типов - и читабельность резко ухудшается.

Функция `structs(datastore, entity_name)` позволяет определить сколько различных структур существует в базе данных и подсчитать количество объектов, соответствующих каждой структуре. Поиск различия структур данных базируется на расчете контрольной суммы для каждой структуры. Функция возвращает словарь, состоящий из структур данных и количества записей, которые соответствуют этой структуре 

Код:

    :::python
    structs_vers = structs(ds, entity_name) 
    for k in structs_vers.keys():
        print k,structs_vers[k][1] 

Вывод (на примере GoogleDevelopers):

    :::text
    e301a0596687d9d4c366cf575d7497ce 228
    43e7965efccaae0fab983d285949ec93 6
    196a0c1928c558a09d9fc7769bed3e35 4
    6391dbd9dd6c006049fc0786b2d33237 65
    624f0edfe9fccc0ba2bcc7f99f90472d 150
    4f9f2f5365293ed83ba310d32c1eca6a 4

Изменив вывод с количества записей _print k,structs_vers[k][1]_ на _print k,structs_vers[k][0]_, можно получить список структур.

