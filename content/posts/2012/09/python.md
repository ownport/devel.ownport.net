Title: Простое создание и управление сервисами в python
Author: d.rey
Date: 2012-09-03 11:36:00
Slug: python
Tags: service,python,pyservice

Несколько недель назад случайно натолкнулся на презентацию [Throwing Together Distributed Services With Gevent](http://www.youtube.com/watch?v=tYk4_Nzl-Gg) небольшого фреймворка для создания сетевых сервисов на python - [ginkgo](https://github.com/progrium/ginkgo). В настоящий момент он ориентирован на работу с [gevent](http://www.gevent.org/) как ядро для работы с сетевыми уровнем и распараллеливание задач. Очень заинтересовала реализация легкого создания и управления сервисами. Не всегда есть необходимость работы с сетью или распараллеливания задач, но есть необходимость переноса компонеты-процесса для работы в фоновом режиме, как сервис. Это и стало идеей несколько видоизменить код ginkgo для того, чтобы уйти от зависимостей gevent, но оставить простоту управления процессами.

pyservice - это небольшая библиотека, позволяющая перенести выполнение процесса в фоновый режим. Запуск/останов процесса осуществляется аналогичным образом, как для любого сервиса в системе

    :::text
    $ pyservice <package.module.class> start|stop|restart|status
    
Ниже приведен пример запуска/останова простого процесса SimpleProcess 
    
    :::text
    $ pyservice tests.processes.simple_process.SimpleProcess
    Starting process with SimpleProcess...
    $
    $ pyservice tests.processes.simple_process.SimpleProcess stop
    Stopping process SimpleProcess...
    $
    $ pyservice tests.processes.simple_process.SimpleProcess restart
    Stopping process SimpleProcess...
    Starting process with SimpleProcess...
    $
    $ pyservice tests.processes.simple_process.SimpleProcess status
    Process SimpleProcess is running, pid: 11082
    
Для того, чтобы процессом можно было управлять через pyservice, он должен быть наследован от pyservice.Process или просто содержать метод run(). Рекомендуются использовать первый вариант.

Пример простого процесса, который просто "засыпает" в фоне на 10 секунд. 
    
    :::python
    import os
    import time
    import pyservice
    
    class SimpleProcess(pyservice.Process):
        
        pidfile = os.path.join(os.getcwd(), 'tests/run/simple_process.pid')
        logfile = os.path.join(os.getcwd(), 'tests/log/simple_process.log')
        
        def run(self):
            time.sleep(10)
    
Поля класса pidfile и logfile позволяют определить файл в котором будут хранится информации о pid процесса и лог-файл соответственно.

В случае, если перед запуском процесса необходимо выполнить некие предварительные действия, для этого нужно переназначить метод do_start(). При остановке процесса выполняется метод do_stop(). В случае, если необходимо выполнить перехват завершения процесса с целью корректного осводождения ресурсов, метод do_stop() нужно переопределить.

[Домашняя страница проекта на github](https://github.com/ownport/pyservice)

