Title: Логирование в web.py и не только
Author: d.rey
Date: 2010-04-27 20:48:00
Slug: webpy
Tags: logging,python,web.py

Для тех, кто уже знаком с web.py небольшой совет [Logging](http://webpy.org/cookbook/logging) на сайте web.py наверняка знаком. В нем рассказывается как организовать логирование HTTP сервера используемого по умолчанию, т.е. developement сервера. Предложенное решение базируется на использовании модуля [wsgilog](http://pypi.python.org/pypi/wsgilog/) и передачей его в ваше приложение через middleware. В этом примере также используется класс LogIO  (это только мое предположение, что LogIO является классом), но проблемка в том, что как он работает и где его взять - не понятно. В библиотеку wsgilog он не входит. Так что если кто-то сможет подсказать где его найти, буду признателен.

В любом случае изюминка этого совета, в этих двух строках:

    :::python
    sys.stdout = LogIO(self.logger, logging.INFO)
    sys.stderr = LogIO(self.logger, logging.ERROR)

Основная идея это переназначение sys.stdout и sys.stderr. Так как LogIO я не нашел, я восстановил его функциональность на свой лад. В итоге получился следующий небольшой класс:

    :::python
    class stream2file:
        ''' Stream wrapper for saving data in file'''
        def __init__(self, filename='stream.data'):
    
            self.fd = open(filename, 'a', 0)
            
        def write(self, data):
            d = data.rstrip() 
            if d == '': return
            self.fd.write("%s\n" % d)
    
        def close(self):
            self.fd.close()

Как видно из листинга, основная работу  выполняет метод write, который записывает данные в файл. Использовать данный класс достаточно просто. На примере запуска web.py приложения в файле index.py:

    :::python
    ...
    if __name__ == "__main__":
        import sys
        
        # redirect stdout and stderr to file
        sys.stdout = sys.stderr = stream2file(filename='webpysrv.log')
    
        app = web.application(urls, globals())
        app.run()

Всю работу выполняет строка _sys.stdout = sys.stderr = stream2file(filename='webpysrv.log')_. После этого, все что попадает в stdout и stderr автоматически перенаправляется в файл webpysrv.log 

Если в ходе отладки необходимо вывести отладочную информацию в лог файл, это можно сделать простым вызовом print в коде. 

Вот так вот просто и wsgilog модуль не нужен.

