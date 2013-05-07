Title: Обработка текста, часть третья
Author: d.rey
Date: 2011-08-05 07:09:00
Slug: blog-post_05
Tags: text,pyflowctrl,python,processing

Используя подходы из предыдущего [поста по обработке данных](http://devel.ownport.net/2011/08/blog-post.html), редко когда бывает достаточным выполнить выборку значений из текста с помощью регулярных выражений. Зачастую данные требуют дополнительной пост-обработки. Для этих целей можно воспользоваться процессом [DictProcessing](http://code.google.com/p/sources-ownport/source/browse/pyflowctrl/processes/textprocessing1.py), позволяющим для каждого поля выполнить свои функции преобразования данных.

Рассмотрим использование процесса на примере: с выхода процесса [Text2Dict](http://code.google.com/p/sources-ownport/source/browse/pyflowctrl/processes/textprocessing1.py) был получен словарь с параметрами:

    :::python
    block = ('msg#1',
        { 
            'datetime': '11/08/02 12:54:03',
            'ip address': 'C0A80101',
            'details': 'No access to IP address',
            'info': '...',
        }
    )
Предположим, что для дальнейшей работы с этими данными необходимо преобразовать: datetime  представить в виде объекта datetime, IP адрес из hex перевести в более привычный вид и сохранить в новом поле ‘ip address (dec)’, поле details нужно оставить без изменений, а поле info удалить. 

    :::python
    from pprint import pprint
    from datetime import datetime
    from processes.textprocessing1 import DictProcessing
    
    def ip_hex2dec(h):
        ''' convert hex ip address to decimal '''
        return '%d.%d.%d.%d' % (int(h[0:2],16), int(h[2:4],16),
                                int(h[4:6],16), int(h[6:8],16))
    
    dict_rules = (
        ('datetime','datetime', lambda d: datetime.strptime(d,'%y/%m/%d %H:%M:%S')),
        ('ip address', 'ip address (dec)', ip_hex2dec),
        ('info', None, None),
    )
    
    dp = DictProcessing(rules=dict_rules)
    pprint(dp.run_once(block))
    
В качестве параметра для обработки словаря block в процесс DictProcessing передается список правил по преобразованию полей. Каждое правило состоит из трех полей: исходное имя поля, результирующее имя поля, функция преобразования. Разберем каждое правило более подробно 
    
    :::python
    ('datetime','datetime', lambda d: datetime.strptime(d,'%y/%m/%d %H:%M:%S')),
    
из словаря берется значение с ключом datetime, преобразуется в объект datetime c помощью lambda функции и сохраняется в словаре под тем же самым именем. 
    
    :::python
    ('ip address', 'ip address (dec)', ip_hex2dec),
    
значение IP адреса из поля ‘ip address’ с помощью функции ip_hex2dec()  преобразуется к виду X.X.X.X и сохраняется в новом поле ‘ip address (dec)’. Поле ‘ip address’ не удаляется.
    
    :::python
    ('info', None, None),
    
для удаления info достаточно указать, что результирующее имя поля и функция преобразования определены как None.

Если поле не указывается в правилах преобразования, оно остается без изменений.

Если необходимо выполнить множественные преобразования над значениями словаря, правила преобразования должны быть указаны в списке в той последовательности, в которой действия над данными должны быть выполнены. Например: 
    
    :::python
    ('text', 'text', remove_spaces),  # удаление лишних пробелов
    ('text', 'text', conv2uppercase),  # все символы в тексте будут 
                                       # представлены заглавными буквами
                                       
