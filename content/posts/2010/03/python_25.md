Title: Код, который печатает себя на python
Author: d.rey
Date: 2010-03-25 06:59:00
Slug: python_25
Tags: python,logic,game

Когда-то [мой друг](http://www.syspulse.com/~andrey/blog/index.htm) задал мне задачку: написать код, который печатает себя, но без использования печати текста из файла. Результат получился следующий: 

    :::python
    #!/usr/bin/python
    body= ['#!/usr/bin/python', '#', 'for line in body:', '   if line == "#": print "body=",body', '   else: print line']
    for line in body:
       if line == "#": print "body=",body
       else: print line

При запуске скрипта на экран выводится точно такой же код, как видно в листинге выше

Если кто-то может подсказать более изящное решение, буду признателен.
