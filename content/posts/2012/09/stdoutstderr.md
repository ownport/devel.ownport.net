Title: Переназначение стандартных потоков stdout/stderr для логирования
Author: d.rey
Date: 2012-09-05 10:34:00
Slug: stdoutstderr
Tags: stdout,logging,python. stderr

Продолжение темы, изначально затронутой в посте ["Логирование в web.py и не только"](http://devel.ownport.net/2010/04/webpy.html). Переназначение стандартных потоков stdout и stderr в файл с помощью модуля logging. 

    :::python
    import sys
    import logging
    
    class Stream2Logger(object):
        def __init__(self, logger, level=logging.INFO):
            self.logger = logger
            self.level  = level
            
        def write(self, data):
            for line in data.splitlines():
                self.logger.log(self.level, line.rstrip())
    
    sys.stdout = sys.stderr = Stream2Logger(logging.getLogger('stdout/stderr'), logging.INFO)

Приведенный выше код, является модификацией кода из поста[ Redirect stdout and stderr to a logger in python](http://www.electricmonk.nl/log/2011/08/14/redirect-stdout-and-stderr-to-a-logger-in-python/)
