Title: Вывод списка файлов из директорий
Author: d.rey
Date: 2011-07-20 06:58:00
Slug: blog-post
Tags: walker,file,directory,find,pyflowctrl,python,tree

Работая с большим числом файлов время от времени возникает необходимость получения список всех файлов из определенной директории с учетом вложенности их в поддиректории. Для этих целей в [pyflowctrl](http://code.google.com/p/sources-ownport/source/browse/pyflowctrl) добавлен новый процесс [TreeWalker](http://code.google.com/p/sources-ownport/source/browse/pyflowctrl/processes/treewalker1.py)

На вход процесса TreeWalker (входной поток input) передаются директории, на выходе (выходной поток output) получаем список файлов и директорий, содержащихся в исходных директориях.

Пример использования: 

    :::python
    from core2 import EmptyStream
    from processes.treewalker1 import TreeWalker
    
    walker = TreeWalker()
    walker.io['input'].put('/home/user1/data/')
    while True:
        try:
            walker.run_once()
            print walker.io['output'].get()
        except EmptyStream:
            break
