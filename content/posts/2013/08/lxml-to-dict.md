Title: Простое преобразование XML в словарь на python
Author: d.rey
Date: 2013-08-23 09:31:00
Slug: lxml-to-dict
Tags: python, lxml, xml, dictionary

Иногда для работы на python с XML файлами небольшого размера проще представить их в виде словаря.
Небольшой код, который позволяет это достаточно просто сделать

    :::python
    from lxml import etree
    
    def etree_to_dict(t):

        d = {t.tag : map(etree_to_dict, t.iterchildren())}
        d.update(('@' + k, v) for k, v in t.attrib.iteritems())
        text = t.text.strip()
        if text:
            d['text'] = text
        return d
    
    xml_root = etree.parse(source_xml).getroot()
    print(etree_to_dict(xml_root))

Исходный код на [stackoverflow](http://stackoverflow.com/questions/7684333/converting-xml-to-dictionary-using-elementtree)
