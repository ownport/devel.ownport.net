Title: html2txt: очистка html документа от тегов
Author: d.rey
Date: 2011-07-26 06:17:00
Slug: html2txt-html
Tags: conversion,text,python,html

Для случаев, когда необходимо очистить html документов от тегов и сформировать тестовый документ, можно использовать небольшую функцию html2txt. В качестве параметра передается содержимое html документа, на выходе получаем текст очищенный от тегов. 

    :::python
    #!/usr/bin/env python
    # -*- coding: utf-8 -*-
    #
    # html2txt
    #
    import re
    
    def html2txt(html):
        """Convert the html to raw txt """
        content = html
    
        p = re.compile('(<p.*?>)|(<tr.*?>)', re.I)
        t = re.compile('<td.*?>', re.I)
        comm = re.compile('<!--.*?-->', re.M)
        tags = re.compile('<.*?>', re.M)
        abbrv = re.compile('&[#\w\d]+;')    
        
        # replace abbreviation by space
        content = abbrv.sub(' ', content) 
        # remove returns time this compare to split filter join
        content = content.replace('\n', '') 
        # replace p and tr by \n
        content = p.sub('\n', content)
        # replace td by \t
        content = t.sub('\t', content) 
        # remove comments
        content = comm.sub('', content) 
        # remove all remaining tags
        content = tags.sub('', content) 
        # remove running spaces this remove the \n and \t
        content = re.sub('\ +', ' ', content) 
        return content

**Ссылки**

- html2text, [http://www.aaronsw.com/2002/html2text/](http://www.aaronsw.com/2002/html2text/)

