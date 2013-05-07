Title: Стандартные потоки
Author: d.rey
Date: 2011-06-20 20:33:00
Slug: blog-post_20
Tags: stdin,stdout,pyflowctrl,python,stderr

Достаточно много постов было посвящено работе ядра небольшой библиотеки pyflowctrl. Пришло время постепенно расширить ее возможности. А именно научить работать со стандартными потоками ввода-вывода: [stdin, stdout, stderr](http://en.wikipedia.org/wiki/Standard_streams).  Про них так много написано, что вряд ли стоит что-либо добавлять. Остановимся лучше более подробно на трех классах, которые реализуют интерфейс к этим потокам.

**class StandardInputStream(Stream)**

используется для получения данных из стандартного ввода. Читает данные строками. В настоящий момент поддерживает получение только текстовых данных.

**class StandardOutputStream(Stream)**

используется для записи данных в стандартный поток вывода. Ничего не добавляет к данным, записывает только те данные, которые переданы. Данные могут быть как текстовые, так и бинарные.

**class StandardErrorStream(Stream)**

используется для записи данных в стандартный поток ошибок. Ничего не добавляет к данным, записывает только те данные, которые переданы. Данные могут быть как текстовые, так и бинарные. Но обычно в данный поток передают именно текстовые данные - вывод диагностических или отладочных сообщений.

Пример использования доступен в репозитории pyflowctrl - [example7.py](https://code.google.com/p/sources-ownport/source/browse/pyflowctrl/examples/example7.py)

    :::python
    class IOProcess(Process):
        def __init__(self):
           super(IOProcess, self).__init__()
               self.io = {
                   'input': StandardInputStream(),
                   'output1': StandardOutputStream(),
                   'output2': StandardErrorStream(),
               }
       
        def main(self):
               while True:
                   line = self.io['input'].get()
                   self.io['output1'].put('stdout: %s' % line)
                   self.io['output2'].put('stderr: %s' % line)
                   yield

В данном примере данные посупившие из стандартного ввода дублируются в потоки вывода и ошибок.

