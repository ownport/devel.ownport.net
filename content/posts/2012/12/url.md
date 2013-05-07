Title: Преобразование относительных URL в абсолютные 
Author: d.rey
Date: 2012-12-09 05:44:00
Slug: url
Tags: conversion,url,python,relative,absolute

Небольшой скрипт на python [rel2abs.py](https://github.com/ownport/my-notes/blob/master/python/snippets/rel2abs.py) позволяющий выполнить преобразование относительных URL в абсолютные.

`rel2abs(<html_code>, <base_url>)`

Пример:

    :::python
    >>> from rel2abs import rel2abs
    >>> rel2abs('<a href="/page/1">', 'http://www.example.com')
    ''<a href="http://www.example.com/page/1">'
    
