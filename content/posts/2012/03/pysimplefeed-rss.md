Title: PySimpleFeed: компактное представление структуры RSS новостей
Author: d.rey
Date: 2012-03-31 08:03:00
Slug: pysimplefeed-rss
Tags: python,simple,feed,rss

В [прошлом посте](http://devel.ownport.net/2012/03/blog-post.html) приводился  пример вывода агрегированной структуры rss постов для нескольких десятков тысяч постов. Даже на таком сравнительно небольшом наборе данных становится понятно, насколько может быть разнообразна и избыточна структура RSS/Atom данных. Небольшая библиотека [PySimpleFeed](http://code.google.com/p/sources-ownport/source/browse/rssfetch/pysimplefeed.py) позволяет привести структуру данных, полученных с помощью [feedparser.feed()](http://code.google.com/p/feedparser/) к плоскому виду, убрать избыточноть, сократить число необходимых для анализа полей с нескольких сотен до десятков. Пример агрегированной структуры из прошлого поста с использование PySimpleFeed выглядит следующим образом: 

    :::python
    {u'entry.authors': 18734,
     u'entry.comments': 7556,
     u'entry.content.html': 7647,
     u'entry.content.rights': 129,
     u'entry.content.subtitle': 256,
     u'entry.content.text': 166,
     u'entry.content.xml': 2,
     u'entry.edited': 239,
     u'entry.language': 354,
     u'entry.links': 18734,
     u'entry.media.urls': 18734,
     u'entry.published': 18492,
     u'entry.publisher': 129,
     u'entry.summary': 18658,
     u'entry.tags.scheme': 8045,
     u'entry.tags.term': 14142,
     u'entry.title': 18734,
     u'entry.updated': 1871}
    Total feed entries: 18734
    
_Примечание_: напротив каждого поля указывается число повторений его в структуре для 18 тыс новостей

В качестве аргументов для SimpleFeed передается исходный словарь и “черный” список полей, которые при обработке будут отбрасываться. Метод handle() возвращает результируюший “плоский словарь”. 

    :::python
    simple_feed = pysimplefeed.SimpleFeed(entry).handle()
    
По умолчанию, “черный” список полей можно не указывать, тогда при обработке будут использованы поля заданные в переменной pysimplefeed.BLACK_LIST_FIELDS.

Детально по каждому из полей, возвращаемых по SimpleFeed(...).handle()

**entry.authors**

список авторов новости. Для каждого автора формируется словарь из полей name (имя автора), href (ссылка на веб сайт), email. Заполняется в зависимости от информации содержашейся в исходных данных

**entry.title**

заголовок новости

**entry.comments**

ссылка на комментарии к новости

**entry.content.subtitle**

дополнительная информация для заголовка новости

**entry.content.html**

текст новости в формате HTML

**entry.content.text**

новость в тектовом формате

**entry.content.xml**

текст новости в формате XML

**entry.content.rights**

информация об авторских правах на новость

**entry.edited**

дата и время последней редакции

**entry.language**

язык на котором написана новость

**entry.links**

список ссылок. Первая ссылка simple_feed[u’entry.links’][0] содержит линк на саму новость. Может быть использована для проверки уникальности в базе данных

**entry.media.urls**

список ссылок на медиа данные: изображения, видео, аудо материалы

**entry.published**

дата и время публикации

**entry.publisher**

издатель

**entry.summary**

краткое описание новости

**entry.tags.term**

список тегов к новости

**entry.tags.scheme**

описание схем для тегов

**entry.updated**

дата и время обновления новости
