Title: pyactors - модель акторов на python
Author: d.rey
Date: 2013-02-21 21:12:00
Slug: pyactors-python
Tags: actors,python,pyactors,intro

Предыдущий пост о модели акторов стал началом небольшого экспериментального проекта [pyactors](https://pypi.python.org/pypi/pyactors/) - реализация модели акторов на python. Этот проект вернул к жизни наработки одного из ранних проектов [pyflowctrl](http://code.google.com/p/pyflowctrl/) и стал частью функционала pyactors. Главная идея акторов хорошо раскрыта в посте "[Модель вычислений на базе акторов (видео)](http://devel.ownport.net/2013/01/blog-post.html)", поэтому остановлюсь лишь на специфике библиотеки pyactors.

Библиотека доступна на github [ownport/pyactors](https://github.com/ownport/pyactors), а также через PyPi. Установка через pip:

    :::text
    $ pip install pyactors
    
Базовым классом для создания акторов является класс Actor. Он определяет набор стандартных методов и свойств по управлению работой актора: name, processing, waiting, start(), stop(), parent, add_child(), children, remove_child(), run(), loop(), supervise(), send(), find().

Основным методом является метод loop(), в нем описывается основная логика работы актора. Следует отметить, что pyactors не умеет автоматически распределять задачи между акторами. За это отвечает разработчик. 

Акторы могут создавать новых акторов с помощью метода add_child() либо удалять их с помощью remove_child(). Наличие у актора своих акторов-потомков влияет на его внутреннюю логику работы. Для случая, когда потомков нет, актор выполняет роль обработчика данных: получает сообщения, обрабатывает, отсылает сообщения другим акторам. Для случаев, когда у актора есть потомки, он выполняет роль супервайзера, а обработкой данных (процессинга) занимаются акторы-потомки.

В настоящий момент библиотека pyactors поддерживает 4 основных подхода для работы акторов: generator, greenlet, thread и process. Производной этих подходов является 5 типов акторов:

- `GeneratorActor` - базируется на стандартных генераторах python
- `GreenletActor` - использует библиотеку gevent
- `ThreadedGeneratorActor` - GeneratorActor, но работающий в отдельном потоке
- `ForkedGeneratorActor` - GeneratorActor, но работающий в отдельном процессе
- `ForkedGreenletActor` - GreenletActor, но работающий в отдельном процессе

GeneratorActor является основым типом. Самый простой, не требует установки дополнительных библиотек. Основывается на том, что в определенный момент времени выполняется только один актор, остальные, находятся в режиме ожидания. Обеспечивается псевдо-параллельность работы. Разработчик сам отвечает за эффективность переключения задач между акторами. Для передачи управления другим актором используется yield внутри метода loop().

Пример простого актора на основе GeneratorActor. 

    :::python
    class TestActor(GeneratorActor):
        ''' TestActor
        '''
        def loop(self):
            ''' loop
            '''
            for i in range(10):
                if self.processing:
                    self.parent.send(i)
                else:
                    break
                yield
            self.stop()
    
Тот же самый актор, но на базе GreenletActor будет иметь следующий вид: 

    :::python
    class TestActor(GreenletActor):
        ''' TestActor
        '''
        def loop(self):
            ''' loop
            '''
            for i in range(10):
                if self.processing:
                    self.parent.send(i)
                else:
                    break
                self.sleep()
            self.stop()
    
Как видно из листинга выше, отличие в использовании generator или greenlet подходов, только в методе переключения между акторами. В первом случае это yield, во втором - self.sleep(). Для использования GreenletActor необходима установить библиотеку [gevent](http://www.gevent.org/).

Использование классов ThreadedGeneratorActor, ForkedGeneratorActor, ForkedGreenletActor ничем не отличается от предыдущих примеров. Для generator используется yield в loop(), для greenlet - self.sleep(). При запуске threaded акторов - актор будет запущен в отдельном потоке, для forked в отдельном процессе. Так как greenlet нельзя запускать в отдельном потоке, поддержка greenlet выполняется только в основном процессе либо отдельном, созданном через наследование ForkedGreenletActor.

Пример запуска актора:

    :::python
    actor = TestActor()
    actor.start()
    actor.run()
    actor.stop()
    
Большое число примеров использования различных типов акторов можно найти в юнит-тестах для [pyactors](https://github.com/ownport/pyactors/tree/master/tests)


