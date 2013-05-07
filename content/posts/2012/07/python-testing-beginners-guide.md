Title: Книга Python Testing: Beginner's Guide
Author: d.rey
Date: 2012-07-11 08:51:00
Slug: python-testing-beginners-guide
Tags: unittest,python,testing,doctest,book

Несколько дней назад прочел книгу Python Testing: Begginners Guide. Если говорить в целом, то общее впечатление от книги положительное. Если уже есть опыт работы с doctest и unittest, первые главы книги можно пропустить. Самое интерестное для себя нашел в главах Developing a Test-Driven Project и Integration Testing and System Testing. Именно они и сформировали или выровняли общее впечатение от книги до положительного. 

![](http://1.bp.blogspot.com/-mOHaiAOCOpg/T_0StiOD_4I/AAAAAAAAAg4/TuxT6RZk1jQ/s320/python-testing-beginners-guide-cover.png)

Хорошим напоминание было использование doctest не как его описывают в классических примерах, когда тесты включаются в состав основного кода, а в отдельном файле - спецификации. Как пример использования, можно взглянуть на спецификацию к библиотеке simplefetch. 

[Спецификация](https://github.com/ownport/simplefetch/blob/develop/tests/specs.md) представляет собой [обычный тестовый файл в формате Markdown](https://raw.github.com/ownport/simplefetch/develop/tests/specs.md), где doctest включены как примеры использования библиотеки к основному тексту спецификации. Протестировать работу библиотеки можно запустив файл спецификации на исполнение командой 

    :::text
    # python -m doctest tests/specs.md

Если в ходе тестирования не произшло исключительных ситуаций или ошибок произойдет возврат к командной строке без каких-либо уведомлений. Если есть необходимоcть в получении детальной информации  о ходе выполнения каждого теста необходимо указать аргумент -v

    :::text
    # python -m doctest -v tests/specs.md
    …
    21 tests in 1 items.
    21 passed and 0 failed.
    Test passed.
    
Мне кажется такое использование doctest наиболее удобным и эффективным, нет необходимости перегружать код и установить более тесную связь между исходным кодом и документацией к нему.

**Ссылки**

- [Купить книгу на www.packtpub.com](http://www.packtpub.com/python-testing-beginners-guide/book)
- [Купить книгу на amazon.com](http://www.amazon.com/Python-Testing-Beginners-Daniel-Arbuckle/dp/1847198848)
- [Книга Python Testing Cookbook](http://www.amazon.com/Python-Testing-Cookbook-Greg-Turnquist/dp/1849514666/ref=pd_sim_b_2)
- [Книга Expert Python Programming: Best practices for designing, coding, and distributing your Python software](http://www.amazon.com/Expert-Python-Programming-practices-distributing/dp/184719494X/ref=pd_sim_b_5)
- [Foundations of Agile Python Development (Expert's Voice in Open Source)](http://www.amazon.com/Foundations-Python-Development-Experts-Source/dp/1590599810/ref=pd_sim_b_1)

