Title: Работа с текстовыми файлами небольшого объема
Author: d.rey
Date: 2011-07-22 06:41:00
Slug: blog-post_22
Tags: reader,file,pyflowctrl,python

В одном из предыдущих постов “[Работа с текстовыми файлами](http://devel.ownport.net/2011/06/blog-post_24.html)” рассматривался подход открытия больших текстовых файлов. Для случаев, когда текстовых файлов много, а объем их относительно не велик (скажем, до 500кб), использовать процесс для чтения файлов из упомянутого выше поста не совсем удобно. Для этого лучше воспользоваться процессом [FileReader](http://code.google.com/p/sources-ownport/source/browse/pyflowctrl/processes/filereader1.py)

В входной поток ‘input’ процесса, FileReader передается путь к файлу. Процесс проверяет существует ли данный файл. В случае успеха, в выходной поток ‘output’ передается список из двух значений: путь к файлу и содержимое самого файла. В случае, если файл не существует информация об этом заносится в поток ошибок ‘error’.

Например необходимо вывести на экран содержимое всех файлов из директории /data/log/:

    :::python
    from core2 import EmptyStream, ProcessFlow
    from processes.treewalker1 import TreeWalker
    from processes.filereader1 import FileReader
    from processes.stdoutprinter1 import Printer
    
    flow = ProcessFlow()
    id_tw = flow.new(TreeWalker())
    id_fr = flow.new(FileReader())
    id_print = flow.new(Printer())
    
    flow.link({'sid': id_tw, 'son': 'output', 'tid': id_fr, 'tin': 'input'})
    flow.link({'sid': id_fr, 'son': 'output', 'tid': id_print, 'tin': 'input'})
    
    flow.pmap[id_tw].io['input'].put('/data/logs/')
    flow.run()
    
Создается поток процессов flow = ProcessFlow(), добавляются три процесса: 

- [TreeWalker](http://devel.ownport.net/2011/07/blog-post.html), для получения списка файлов из директорий
- [FileReader](http://code.google.com/p/sources-ownport/source/browse/pyflowctrl/processes/filereader1.py), для чтения содержимого файлов
- [Printer](http://code.google.com/p/sources-ownport/source/browse/pyflowctrl/processes/stdoutprinter1.py), вывод содержимого файлов в стандартный вывод (stdout)

Входные и выходные потоки процессов объединяются между собой с помощью [flow.link()](http://devel.ownport.net/2011/05/pyflowctrl-c-python.html). На вход процесса TreeWalker передается исходная директория. flow.run() запускает на выполнение поток процессов.
