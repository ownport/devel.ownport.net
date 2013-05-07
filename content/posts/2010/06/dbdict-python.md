Title: dbdict - база данных ключ/значение на python
Author: d.rey
Date: 2010-06-28 18:16:00
Slug: dbdict-python
Tags: key-value,python,database

Некоторое время назад я писал о небольших наработках использования баз данных ключ/значение: [kvlite или KeyValue datastore на базе sqlite3](http://devel.ownport.net/2010/01/kvlite-keyvalue-datastore-sqlite3.html), [методы хранения данных](http://devel.ownport.net/2010/06/blog-post.html). Сегодня я хочу продолжить эту тематику и рассказать о маленькой python библиотеке для работы с ключ/значения базой данных  использующей sqlite3 как ядро.

Библиотека называется dbdict и доступна на bitbucket.org http://www.bitbucket.org/nephics/dbdict. По размеру составляет всего 400 строк. Интерфейс работы с библиотекой достаточно прост и легко может быть продемонстрирован следующим кодом:

    :::python
    import dbdict
    d = dbdict.open('tempdict')
    d['foo'] = 'bar'
    # В этой точке пара ключ-значение 'foo': 'bar' будет сохранена на диск.
    d['John'] = 'doh!'
    d['pi'] = 3.999
    d['pi'] = 3.14159  # замещение предыдущей версии pi
    d['pi'] += 1
    d['age'] = '21'
    del d['age']    # пара 'age': 21 будет удалена из базы
    d.close()    # закрытие файла база данных

Для более эффективного получения значений из базы данных может быть использован метод get(), который в качестве аргумента получает список ключей. Для эффективного добавления/обновления пар ключ-значение используется метод update(). В качестве аргумента этот метод получает список пар ключ-значение. Если пара не существует в базе она будет добавлена, если существует, то значение для указанного ключа будет изменено. Для удаления используется метод remove, который также удобно использовать над множеством ключей. 

Также есть ряд служебных методов: clear - удаление всех записей и освобождение неиспользуемого дискового пространства, reindex - пересоздание индекса ключей, vacuum - [стандартная функция sqlite3](http://www.sqlite.org/lang_vacuum.html) 

К сожалению у меня не получилось воспроизвести результаты тестов, проводимых Якобом и достичь записи 1 млн. пар за 12 секунд, и чтения 1 млн. записей за 32 секунд и 10 секунд при использовании метода get(). Думаю это можно списать на мой лептоп (Lenovo X61s), который на лету шифрует данные при записи на диск. Да и процессор у меня не из самых мощных. Даже несмотря на эти ограничения системы, результаты получились на такие уж и плохие:

**Время выполнения вставки/обновления, выборки ключей (база данных на диске)**

<table border="1" bordercolor="#000000" cellpadding="3" cellspacing="0" id="ku:0" style="border-collapse: collapse; font-family: Verdana; font-size: 12px; line-height: inherit;">
<tbody>
    <tr style="text-align: left;">
        <td width="16.666666666666668%"></td>
        <td width="16.666666666666668%">100 записей</td>
        <td width="16.666666666666668%">1000 записей</td>
        <td width="16.666666666666668%">10000 записей</td>
        <td width="16.666666666666668%">100000 записей</td>
        <td width="16.666666666666668%">1000000 записей</td>
    </tr>
    <tr style="text-align: left;">
        <td width="16.666666666666668%">INSERT/REPLACE</td>
        <td width="16.666666666666668%">0.33 сек</td>
        <td width="16.666666666666668%">0.61 сек</td>
        <td width="16.666666666666668%">0.73 сек</td>
        <td width="16.666666666666668%">5 сек</td>
        <td width="16.666666666666668%">53.16 сек</td>
    </tr>
    <tr style="text-align: left;">
        <td width="16.666666666666668%">SELECT (по одному значению)</td>
        <td width="16.666666666666668%">0.03 сек</td>
        <td width="16.666666666666668%">0.25 сек</td>
        <td width="16.666666666666668%">2.5 сек</td>
        <td width="16.666666666666668%">25.69 сек</td>
        <td width="16.666666666666668%">261.37 сек</td>
    </tr>
    <tr style="text-align: left;">
        <td width="16.666666666666668%">SELECT (методом get)</td>
        <td width="16.666666666666668%">0 сек</td>
        <td width="16.666666666666668%">0.03 сек</td>
        <td width="16.666666666666668%">0.281 сек</td>
        <td width="16.666666666666668%">3.20 сек</td>
        <td width="16.666666666666668%">42.95 сек</td>
    </tr>
</tbody>
</table>

**Расчетное значение количества записей в секунду (база данных на диске)**

<table border="1" bordercolor="#000000" cellpadding="3" cellspacing="0" id="eb1v" style="border-collapse: collapse; font-family: Verdana; font-size: 12px; line-height: inherit;">
<tbody>
    <tr style="text-align: left;">
        <td width="16.666666666666668%"></td>
        <td width="16.666666666666668%">100 записей</td>
        <td width="16.666666666666668%">1000 записей</td>
        <td width="16.666666666666668%">10000 записей</td>
        <td width="16.666666666666668%">100000 записей</td>
        <td width="16.666666666666668%">1000000 записей</td>
    </tr>
    <tr style="text-align: left;">
        <td width="16.666666666666668%">INSERT/REPLACE</td>
        <td width="16.666666666666668%">304.88</td>
        <td width="16.666666666666668%">1642.04</td>
        <td width="16.666666666666668%">13623.98</td>
        <td width="16.666666666666668%">20000</td>
        <td width="16.666666666666668%">18812.2</td>
    </tr>
    <tr style="text-align: left;">
        <td width="16.666666666666668%">SELECT (по одному значению)</td>
        <td width="16.666666666666668%">3225.81</td>
        <td width="16.666666666666668%">4000</td>
        <td width="16.666666666666668%">4000</td>
        <td width="16.666666666666668%">3893.02</td>
        <td width="16.666666666666668%">3825.92</td>
    </tr>
    <tr style="text-align: left;">
        <td width="16.666666666666668%">SELECT (методом get)</td>
        <td width="16.666666666666668%">-</td>
        <td width="16.666666666666668%">32258.06</td>
        <td width="16.666666666666668%">35587.19</td>
        <td width="16.666666666666668%">31210.99</td>
        <td width="16.666666666666668%">23281.26</td>
    </tr>
</tbody>
</table>

**Время выполнения вставки/обновления, выборки ключей (база данных в памяти)**

<table border="1" bordercolor="#000000" cellpadding="3" cellspacing="0" id="jt6q" style="border-collapse: collapse; font-family: Verdana; font-size: 12px; line-height: inherit;">
<tbody>
    <tr style="text-align: left;">
        <td width="16.666666666666668%"></td>
        <td width="16.666666666666668%">100 записей</td>
        <td width="16.666666666666668%">1000 записей</td>
        <td width="16.666666666666668%">10000 записей</td>
        <td width="16.666666666666668%">100000 записей</td>
        <td width="16.666666666666668%">1000000 записей</td>
    </tr>
    <tr style="text-align: left;">
        <td width="16.666666666666668%">INSERT/REPLACE</td>
        <td width="16.666666666666668%">0 сек</td>
        <td width="16.666666666666668%">0.03 сек</td>
        <td width="16.666666666666668%">0.37 сек</td>
        <td width="16.666666666666668%">4.19 сек</td>
        <td width="16.666666666666668%">46.812 сек</td>
    </tr>
    <tr style="text-align: left;">
        <td width="16.666666666666668%">SELECT (по одному значению)</td>
        <td width="16.666666666666668%">0 сек</td>
        <td width="16.666666666666668%">0.05 сек</td>
        <td width="16.666666666666668%">0.5 сек</td>
        <td width="16.666666666666668%">4.9 сек</td>
        <td width="16.666666666666668%">54.7 сек</td>
    </tr>
    <tr style="text-align: left;">
        <td width="16.666666666666668%">SELECT (методом get)</td>
        <td width="16.666666666666668%">0 сек</td>
        <td width="16.666666666666668%">0.03 сек</td>
        <td width="16.666666666666668%">0.25 сек</td>
        <td width="16.666666666666668%">2.83 сек</td>
        <td width="16.666666666666668%">34.47 сек</td>
    </tr>
</tbody>
</table>

**Расчетное значение количества записей в секунду (база данных в памяти)**

<table border="1" bordercolor="#000000" cellpadding="3" cellspacing="0" id="jt6q" style="border-collapse: collapse; font-family: Verdana; font-size: 12px; line-height: inherit;">
<tbody>
    <tr style="text-align: left;">
        <td width="16.666666666666668%"></td>
        <td width="16.666666666666668%">100 записей</td>
        <td width="16.666666666666668%">1000 записей</td>
        <td width="16.666666666666668%">10000 записей</td>
        <td width="16.666666666666668%">100000 записей</td>
        <td width="16.666666666666668%">1000000 записей</td>
    </tr>
    <tr style="text-align: left;">
        <td width="16.666666666666668%">INSERT/REPLACE</td>
        <td width="16.666666666666668%">-</td>
        <td width="16.666666666666668%">32258.06</td>
        <td width="16.666666666666668%">26666.67</td>
        <td width="16.666666666666668%">23883.45</td>
        <td width="16.666666666666668%">21362.04</td>
    </tr>
    <tr style="text-align: left;">
        <td width="16.666666666666668%">SELECT (по одному значению)</td>
        <td width="16.666666666666668%">-</td>
        <td width="16.666666666666668%">21276.59</td>
        <td width="16.666666666666668%">20661.16</td>
        <td width="16.666666666666668%">20383.20</td>
        <td width="16.666666666666668%">18280.2</td>
    </tr>
    <tr style="text-align: left;">
        <td width="16.666666666666668%">SELECT (методом get)</td>
        <td width="16.666666666666668%">-</td>
        <td width="16.666666666666668%">32258.06</td>
        <td width="16.666666666666668%">40000</td>
        <td width="16.666666666666668%">35348.18</td>
        <td width="16.666666666666668%">29012.42</td>
    </tr>
</tbody>
</table>

Код на основании которого проводились тесты:

    :::python
    d = DbDict2(':memory:') # or d = DbDict2('dbdict2.sqlite')
    
    data = []
    keys = []
    time_start = datetime.datetime.now()
    for i in range(10**6): 
        key = unicode("key-%d" % i,'utf-8')
        value = unicode("value-%d" % i,'utf-8')
        data.append((key, value))
        keys.append(key)
    print 'Data generation time: %s' % (datetime.datetime.now() - time_start)
    
    time_start = datetime.datetime.now()
    d.update(data)
    print 'Data insert/replace time: %s' % (datetime.datetime.now() - time_start)
    
    time_start = datetime.datetime.now()
    for k in keys: v = d[k]
    print 'Data select time: %s' % (datetime.datetime.now() - time_start)
    
    time_start = datetime.datetime.now()
    d.get(keys)
    print 'Data select time (method get()): %s' % (datetime.datetime.now() - time_start)
    d.close()
    
Так же следует отметить, что исходный код bddict не позволял делать выборку методом get при передаче в качестве аргумента миллиона записей, пришлось его несколько доработать.

    :::python
    def get(self, keys):
        '''Get item(s) for the specified key or list of keys.
        
        Items will be returned only for those keys that are defined. The
        function will pass silently (i.e. not raise an error) if one or more of
        the keys is not defined.
        
        Support long list of keys'''
    
        ssize = 200  # slice size
        try:
            keys = tuple(keys)
        except TypeError:
            # probably a single key (ie not an iterable)
            keys = (keys,)
        SQL = u'SELECT key, value FROM data WHERE key in (%s);'
        kv = []
        for i in xrange(len(keys)/ssize + 1):
            parted_keys = keys[i*ssize:i*ssize+ssize]
            kv.extend(self.con.execute(SQL % ','.join('?' for k in parted_keys), parted_keys).fetchall())
        return kv
        
Как резюме, в целом библиотека достаточно интересная, простая и удобная в применении. В будущем постараюсь поделиться опытом использования ее в реальных приложениях.
