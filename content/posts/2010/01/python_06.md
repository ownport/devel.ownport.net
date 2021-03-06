Title: Тождественность в python на примере списков
Author: d.rey
Date: 2010-01-06 13:44:00
Slug: python_06
Tags: python,identity

По своему опыту могу сказать, что большинство сложных проблем решаются достаточно простыми решениями. Но для этого нужно знать специфику и особенности.

Важно помнить, что в python несколько переменных могут ссылаться на один и тот же объект. Это одна сторона медали, другая: переменные могут ссылаться на объекты, которые выглядят похожими (у них одинаковый тип и значение), но они не тождественны.

Приведу на примере

    :::python
    >>> a = [1,2,3,4]
    >>> b = a
    >>> id(a)
    16762120
    >>> id(b)
    16762120

Как видно из листинга, переменные a и b ссылаются на один и тот же объект [1,2,3,4]. Теперь если изменить переменную b, путем добавления нового элемента, мы получим

    :::python
    >>> b.append(5)
    >>> id(a)
    16762120
    >>> id(b)
    16762120
    >>> b
    [1, 2, 3, 4, 5]
    >>> a
    [1, 2, 3, 4, 5]

Видно, что изменение переменной b приводит также к изменению и переменной a. 

Если мы сделали 'b' равным 'a' (b=a) для того чтобы, иметь возможность изменять переменную 'b', а переменную 'a', использовать как резервную копию списка. Данный подход может быть ошибкой, как видно из примера.

Нужно быть аккуратным при написания кода и помнить про эту особенность, чтобы не допускать досадных ошибок.
