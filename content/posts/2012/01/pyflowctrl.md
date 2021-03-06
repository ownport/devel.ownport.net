Title: Информационные пакеты в pyflowctrl
Author: d.rey
Date: 2012-01-07 07:15:00
Slug: pyflowctrl
Tags: process,control,pyflowctrl,python,packet

Основой любой вычислительной системы является обработка информации: получение одних данных из других с пользованием некоторых алгоритмов. Долгое время основной акцент при разработке pyflowctrl делался на работе самих процессов. Когда каждый процесс рассматривается как черный ящик, со временем возникают сложности в организации их совместной работы. Отсутствие механизма согласованного обмена данных не только между двумя процессами, но и между процессами системы в целом, приводит к необходимости постоянно адаптировать существующие процессы под меняющиеся задачи и как следствие гибкость системы теряется.

Приведу пример диаграммы взаимодействия процессов одного из предыдущих постов - '[Извлекаем ссылки из HTML страницы](http://devel.ownport.net/2011/06/html.html)”

![](http://4.bp.blogspot.com/-r8z6wRAmZMM/Tgf1G6NPxmI/AAAAAAAAAX4/1PFCJbrlmkc/s1600/UrlExtract.png)

Ранее на диаграммах отображались только сами процессы, обмен данных между процессами уходил на второй план. В примере выше, цепочка процессов могла работать только в такой конфигурации. Каждый процесс выдавал свой набор выходных данных и следующий за ним процесс мог работать только с этими данными. Если бы понадобилось использовать существующие процессы для других случаев, перед передачей данных в процесс их необходимо было специально обрабатывать, подстраивать под исходные параметры входного потока процесса. Конечно же, если исходить из того, что каждый процесс - это черный ящик, то от этого не уйти. Чаще всего процессы выполняют узконаправленную работу и работают с определенными типами входных и выходных данных.

Больше сложностей создает отсутствие прозрачного пропуска через процесс данных, необходимые не в текущем процессе, но в последующих. Например: на вход системы подается документ, проходя через различные процессы документ изменяет свою структуру. Каждый процесс выполняет над документом только определенные манипуляции, тогда как в конце обработки, необходимо вывести все обработанные данные документа. При существующем подходе pyflowctrl.core3 приходится адаптировать существующие процессы под каждую конкретную задачу. Для того, чтобы избежать подобной необходимости в pyflowctrl.core4 вводится новое понятие - информационный пакет.

Информационный пакет является контейнером, переносчиком данных между процессами. Структура пакета не статична, она может меняться, подстраиваться под конкретные нужды как каждого процесса, так системы обработки данных в целом. 

    :::python
    from pyflowctrl.core4 import Packet
    
    p = Packet()
    # 'корневая” область
    p.id = 1
    p.url = 'http://example.com'
    p.content = '<html><body>test 1</body></html>'
    p.tags = ['html','body']
    
Процесс, получив пакет работает с его 'корневыми” данными: принимает, обрабатывает, изменяют значения в существующих полях или создает новые. Важным моментом при таком подходе является необходимость избежания коллизии имен полей, используемых другими процессами и текущим процессом. Для решения этого вопроса в пакете можно создавать различные пространства имен (namespaces). Перед передачей пакета в процесс, данные, не относящиеся к текущему процессу переносятся из 'корневой” области видимости в отдельное пространство имен. 

    :::python
    # область имен 'test-namespace”
    p.set_namespace('test-namespace”)
    p['test-namespace”].id = 10
    p['test-namespace”].url = 'http://example2.com'
    p['test-namespace”].content = '<html><body>test 2</body></html>'
    p['test-namespace”].tags = ['html','body']
    
Размещая данные в различных пространствах имен можно прозрачно передавать данные, необходимые для следующего процесса.

Пространства имен в пакете можно создавать с помощью метода _set_namespace(name)_, удалять _del_namespace(name)_, выводить список _namespaces()_. 

В связи с тем, что пространства имен могут быть доступны внутри процесса существует риск нарушении структуры пакета самим процессом в случае ошибки или его некорректной работы. Процесс должен работать только с корневыми переменными, а управление данными в пространствах имен выполняется на уровне управления потоками процессов. Число пространств имен внутри пакета не ограничивается.

Часто возникают ситуации, когда процесс при получении одного пакета, должен вернуть несколько. В этом случае, для сохранения данных из пространств имен  входного пакета, процесс должен скопировать _copy()_ исходный пакет и изменить в нем 'корневые” поля.
