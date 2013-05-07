Title: print и sys.stdout функции в многопотоковых приложения
Author: d.rey
Date: 2010-04-26 22:52:00
Slug: print-sysstdout
Tags: logging,python,miltuthread

Я думаю не ошибусь, если скажу, что функция print наиболее часто используемая функция. Именно с нее начинается первое приложение на python - Hello world. Работа с sys.stdout также рассматривается на начальных этапах. Казалось бы, что может быть сложного и необычного в этих в двух подходах записи данных в стандартный вывод?

Для того, чтобы продемонстрировать отличия можно воспользоваться следующих кодом. Идея работы скрипта достаточно проста: запускается 10 потоков, каждый из которых выводит в стандартный вывод по 10 раз строку "line" с номером от 0 до 9.

    :::python
    import sys
    import threading
    
    class PrintThread(threading.Thread):
        def run(self):
            for i in range(10):
                print 'line %d' % i
    
    if __name__ == '__main__':
        for i in range(10):
            PrintThread().start()
            
Для данного кода вывод будет следующим (символ перевода строки умышлено убран для лучшего восприятия):

    :::
    line 0
    line 1line 0
    line 2line 1
    line 3line 2line 0
    line 4line 3line 1
    line 4line 0line 5line 2
    line 5line 6line 0line 1line 3

Видно, что данные выводятся на экран в неупорядоченном виде. Если изменить скрипт и вместо функции print использовать sys.stdout.write

    :::python
    import sys
    import threading
    
    class SysWriteThread(threading.Thread):
        def run(self):
            for i in range(10):
                sys.stdout.write('line %d\n' % i)
                
    if __name__ == '__main__':
        for i in range(10):
            SysWriteThread().start()

Логика скрипта изменена незначительно, а вот вывод изменился достаточно сильно

    :::
    line 0
    line 1
    line 2
    line 0
    line 3
    line 1
    line 4
    line 2
    line 0
    line 5
    line 3
    line 1
    line 6
    line 4
    line 2
    line 7
    line 0
    line 5

Из этих примеров можно сделать вывод, что использование sys.stdout предпочтительней, чем print для многопотоковых приложений. Это позволяет получить лог-файлы более структурировано.  К сожалению узнать природу данного явления  и объяснить подобное поведение более подробно затруднительно, так как print является встроенной функцией, а sys является встроенным модулем. Если кто-то может объяснить почему это работает именно так, всегда рад услышать развернутый ответ.

