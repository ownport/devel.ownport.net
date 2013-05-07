Title: Простое представление словарей со сложной структурой
Author: d.rey
Date: 2012-03-29 22:22:00
Slug: blog-post
Tags: data,structure,python,complex,rss

Словари со сложной внутренней структурой могут содержать различные типы данных от вложенных словарей до списков, содержащих опять же вложенные словари или другие, произвольные типы данных. Примером таких словарей является структура RSS ленты, возвращаемая библиотекой [Universal Feed Parser](http://code.google.com/p/feedparser/) по feedparser.parse(). Определение структуры с множественной вложеностью данных, а тем более сравнение структур между собой может быть достаточно сложной задачей. Для упрощения этих задач можно воспользоваться небольшой функцией, выполняющей преобразование вложенных структур к “плоскому” виду - словарю, содержащему в виде ключа информацию о вложенности данных, а в виде значения - сами данные. 

    :::pyhton
    def dict2flat(root_name, source, removeEmptyFields=False):
        ''' returns a simplified "flat" form of the complex hierarchical dictionary 
        '''
        flat_dict = {}
        if isinstance(source, list):
            for i,e in enumerate(source):
                new_root_name = "%s[%d]" % (root_name,i)
                for k,v in dict2flat(new_root_name,e).items():
                    flat_dict[k] = v
        elif isinstance(source, dict):
            for k,v in source.items():
                new_root_name = "%s.%s" % (root_name, k)
                for kk, vv in dict2flat(new_root_name,v).items():
                    flat_dict[kk] = vv
        else:
            if source is not None:
                flat_dict[root_name] = source
        return flat_dict
        
В качестве параметров передаются имя корня (произвольное текстовое значение), исходный словарь, флаг, определяющий удалять поля с пустыми значениями или нет. 

Пример неполного вывода функции dict2flat(u‘entry’, entry) c агрегацией струкуры данных более сотни RSS лент выглядит следующим образом: 

    :::python
    {u'entry.app_control': 1,
     u'entry.app_edited': 232,
     u'entry.author': 9059,
     u'entry.author_detail.email': 605,
     u'entry.author_detail.href': 1166,
     u'entry.author_detail.name': 8989,
     u'entry.authors[0].email': 338,
     u'entry.authors[0].href': 1166,
     u'entry.authors[0].name': 1600,
     u'entry.bitrate': 25,
     u'entry.comments': 7202,
     u'entry.comments_detail.value': 1,
     u'entry.content[0].base': 3330,
     u'entry.content[0].language': 404,
     u'entry.content[0].type': 3330,
     u'entry.content[0].value': 3330,
     u'entry.content[1].base': 77,
     u'entry.content[1].type': 77,
     u'entry.content[1].value': 77,
     u'entry.dc_date.taken': 420,
     u'entry.dc_relation': 124,
     u'entry.dc_source.url': 1877,
     u'entry.dc_tags': 3765,
     u'entry.dc_tags_detail.value': 1,
     u'entry.embed': 1005,
    ...
    u'entry.link': 17885,
     u'entry.links[0].href': 17885,
     u'entry.links[0].rel': 17885,
     u'entry.links[0].style': 1,
     u'entry.links[0].target': 1,
     u'entry.links[0].title': 365,
     u'entry.links[0].type': 17826,
     u'entry.links[10].href': 2,
    …
     u'entry.tags[0].label': 355,
     u'entry.tags[0].scheme': 7654,
     u'entry.tags[0].term': 13504,
     u'entry.tags[10].label': 5,
     u'entry.tags[10].scheme': 160,
     u'entry.tags[10].term': 450,
     u'entry.tags[11].label': 5,
     u'entry.tags[11].scheme': 151,
    …
    
Напротив каждого поля указывается число повторений его в структуре данных для порядка 18 тыс. RSS новостей.

