Title: Новое ядро (core3) для pyflowctrl
Author: d.rey
Date: 2011-08-15 06:30:00
Slug: core3-pyflowctrl
Tags: core,pyflowctrl,python,processing

Время показало, что версия ядра core2.py имеет недостатки, ограничивающие выполнение ряда операций над данными. Разберем текущую схему реализации:

![](http://2.bp.blogspot.com/-QRViSOmpRnU/TkiQtacq_rI/AAAAAAAAAZE/ddm5pFYWtyk/s1600/core2-sheme.png)

Если у процесса есть входной поток, процесс ожидает поступление из него данных. Отсутствие данных на входе приводит к формированию исключения EmptyStream, и следом за ним исключения StopIteration. Согласно специфики реализации ProcessFlow на базе [core2](http://code.google.com/p/sources-ownport/source/browse/pyflowctrl/core2.py), это событие приведет к останову процесса и некорректной работе для случаев, когда предыдущие в цепочке процессы могут не возвращать данные.

Например:

![](http://2.bp.blogspot.com/-jYq0jspqF7c/TkiRbbDaWZI/AAAAAAAAAZM/ds-lJDYDgIE/s1600/core2-example.png)

В этом случае, как только на процесс Output  не поступят данные, процесс завершит свою работу. Это приведет к ситуации, когда Generator и Filter еще будут работать, а вывод результата будет уже невозможен из-за останова процесса Output.

Необходим новый подход в управлении потоков как данных, так и процессов. Исходная схема для нового ядра core3:

![](http://1.bp.blogspot.com/-LJMmb30ou5c/TkiRhJNK_6I/AAAAAAAAAZU/XxS02NOT29A/s1600/core3-sheme.png)

Новый подход позволяет выполнять контроль работы процессов на основании внутреннего статуса процессов. 

![](http://3.bp.blogspot.com/-0yuj-cq_1zY/TkiRmKtUa4I/AAAAAAAAAZc/gI3pijkDS6w/s1600/core3-process-status.png)

При создании процесс имеет статус NOT_STARTED. После первой итерации, если данных во входном потоке нет, он переходит в статус WAITING. При получении данных и и их обработке, статус процесса меняется на PROCESSING. Когда все процессы переходят в состояние WAITING, работа ProcessFlow завершается.

Пример метода main() для процесса на базе нового ядра core3: 

    :::python
    def main(self):
        while True:
            try:
                data = self.io['input'].get()
            except EmptyStream:
                yield WAITING
                continue
            self.io['output'].put(handle_data(data))
            yield PROCESSING

Часть процессов уже переведена на работу с [core3](http://code.google.com/p/sources-ownport/source/browse/pyflowctrl/core3.py):

- [FileReader](http://code.google.com/p/sources-ownport/source/browse/pyflowctrl/processes/filereader2.py)
- [HTMLSpecials](http://code.google.com/p/sources-ownport/source/browse/pyflowctrl/processes/htmlspecials2.py)
- [TextProcessing](http://code.google.com/p/sources-ownport/source/browse/pyflowctrl/processes/textprocessing2.py)
- [TreeWalker](http://code.google.com/p/sources-ownport/source/browse/pyflowctrl/processes/treewalker2.py)

Остальные будут переводиться по мере необходимости.

