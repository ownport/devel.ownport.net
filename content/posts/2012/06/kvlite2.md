Title: Консоль для kvlite2
Author: d.rey
Date: 2012-06-03 09:28:00
Slug: kvlite2
Tags: kvlite2,datastore,key-value,console

Библиотека [kvlite2](http://code.google.com/p/kvlite/)  используется уже достаточно долгое время для моих личных проектов - для случаев, когда исходные данные структурированы, но имеют изменяемую внутреннюю структуру. Либо когда необходимо организовать временное хранение данных, когда native базы данных ключ/значение установить нельзя (как пример на shared hosting). Индексация поддерживается только по первичному ключу, поэтому чаще всего ключом является результат SHA1 хеш функции от значения поля хранимых данных уникального для коллекции. Если поля с уникальными данными нет, то в качестве ключа можно использовать встроенную функцию вычисления хеш и использовать результат как ключ к данным. Данные представляются собой стандартный словарь python. При сохранении данные сериализуются в строку с помощью стандратной библиотеки python - json, затем строка сжимается с помощью zlib. Как ключ так и значение хранится в MySQL как бинарные последовательности.
 
    :::sql
    CREATE TABLE IF NOT EXISTS collection (
           __rowid__ INT NOT NULL AUTO_INCREMENT PRIMARY KEY,
           k BINARY(20) NOT NULL, 
           v MEDIUMBLOB,
           UNIQUE KEY (k) ) ENGINE=InnoDB;
           
В связи с тем, что данные хранятся в бинарном формате, работать с ними через консоль mysql не просто не удобно, но и просто невозможно. Отсутствие возможности быстрого просмотра данных, приводило к ряду досадных ошибок при работе с библиотекой. Для упрощения работы, в библиотеку была встроена возможность работы с коллекциями kvlite2 через консоль. 

    :::text
    $ python kvlite2.py
    kvlite> ?
    
       help <command> show <command> help
       version  show kvlite version
       licence  show licence
       history  show commands history 
       exit   exit from console 
    
       create <name> <uri> create new collection (if not exists)
       use <collection> use the collection as the default (current) collection
       show collections list of available collections (defined in settings.py)
       remove <collection> remove collection
       import <filename> import collection configuration from JSON file
       export <filename> export collection configurations to JSON file
    
       hash [string] generate sha1 hash, random if string is not defined
       keys   list of keys 
       items  list of collection's items 
       get <key>  show collection entry by key
       put <key> <value> store entry to collection
       delete <key>  delete entry by key 
       count  show the amount of entries in collection 
    kvlite> 

**Краткое описание возможностей**

- Система помощи вызывается с помощью команд `?` и `help`. Без указания команды после `?` и `help` выводится подсказка для всех команд.
- `version` отображает информацию о текущей верси библиотеки
- `licenсe` выводит на экран текст лицензии
- `history`  позволяет отобразить последние 20 команд введенных в консоли. В истории игнорируются повторные команды, но записываются неуспешные или ошибочные команды
- `exit` - завершение работы, выход из консоли

Из консоли можно создать новую коллекцию командой create. В качестве аргументов передается имя коллекции и URI на базу данных в формате `mysql://<username>:<password>@<host>/<database>.<collection>`

Например: 

    :::text
    create kvlite2_test1 mysql://kvlite_test1:kvlite_test1@localhost/kvlite_test1

Перед созданием коллекции необходимо создать базу данных MySQL (если она еще не была создана). Для этого можно воспользоваться следующим скриптом, изменив в нем значения для `<username>`, `<password>`, `<host>`, `<database>`

    :::sql
    /* create database */
    CREATE DATABASE IF NOT EXISTS <database>;
    /* create user for <database> */
    CREATE USER <username>@<host> IDENTIFIED BY <password>;
    /* add <username> user rights for <database> database */
    GRANT ALL ON <database>.* TO <username>@<host>;
    
Для того, чтобы начать работать с данными коллекции необходимо выбрать ее с помощью команды use, указав в качестве агрумента имя коллекции. Например: `use kvlite_test1`. После выполнения команды строка приглашения изменится и в ней будет указано с какой коллекцией в текущий момент будут выполняться команды. 

    :::text
    kvlite>
    kvlite> use kvlite_test1
    kvlite_test1>
    kvlite_test1>

Консоль позволяет одновременно работать с несколько коллекциями, но только одна может быть активна в текущий момент и именно с ней будут проводиться операции `keys`, `items`, `get`, `put`, `delete`, `count`. Вывести список доступных коллекций можно с помощью команды 

    :::text
    kvlite>
    kvlite> show collections
    kvlite_test1
    kvlite_test2
    kvlite>
    
Для того, чтобы добавить коллекцию, можно воспользоваться описанной выше командой create. В случае, если коллекция уже создана в базе данных MySQL, пересоздание коллекции не будет выполняться. Добавится только ссылка на коллекцию. Вывести список достпуных коллекций можно командой show, а выбор коллекции выполнить через use `<collection>`

Команда `remove <collection>` удаляется коллекцию и ссылку на нее в `show collections`

Для коллекций с которыми чаще всего приходится работать можно создать JSON файл и импортировать его с помощью команды `import` 

    :::text
    kvlite>
    kvlite> import collections.json
    Import completed
    kvlite>

Cсылки на коллекции, созданные через консоль с помощью команды `create` можно экспортировать с помощью команды `export <JSON filename>`. Если в качестве аргумента передать существующий JSON файл, он будет перезаписан. Сохраненный список коллекций в дальнейшем можно использовать и перед началом импортировать его в консоль - это позволяет избежать необходимости повторно создавать ссылки на коллекции с помощью команды `create`.

Как отмечалось выше, в качестве ключа данных может используется хеш фунция от уникального поля данных. Расчитать хеш функцию можно с помощью встроенной команды `hash`. Например для случая, когда в качестве уникального ключа используется URL страницы 

    :::text
    kvlite>
    kvlite> hash http://devel.ownport.net
    sha1 hash: b5e7a1c25830edf7d5c21f5db7b7bdf2c5091687
    kvlite>
    
Если в данных уникальных полей нет, хеш для ключа можно сгенерировать с помощью команды `hash` без указания значения 

    :::text
    kvlite>
    kvlite> hash 
    Random sha1 hash: da0060bfeb349655655eedb2b3989eb83ebeeeb6
    kvlite>
     
В качестве исходных данных для генерации псевдо-случайного хеш используется текущее время, результат функции datetime.datetime.now(), поэтому если делать вызов `hash` очень быстро возможны повторы. Так же возможны коллизии, если в тоже время параллельно создавались хеш для ключей на другой машины, но для той же коллекции.

`keys` - вывод списка всех ключей коллекции 

    :::text
    kvlite_test1>
    kvlite_test1> keys
    14d86018060cfcdde7c7a462fca4acb62fbdb151
    14d86018060cfcdde7c7a462fca4acb62fbdb152
    kvlite_test1>

`items` - вывод всей информации о всех документах, хранимой в коллекции 

    :::text
    kvlite_test1>
    kvlite_test1> items
    14d86018060cfcdde7c7a462fca4acb62fbdb151
    {u'key1': u'value1', u'key2': u'value2'}
    
    14d86018060cfcdde7c7a462fca4acb62fbdb152
    {u'key3': u'value3', u'key4': u'value4'}
    kvlite_test1>
    
`count`  позоляет определить количество документов в коллекции 

    :::text
    kvlite_test1>
    kvlite_test1> count
    2
    kvlite_test1>
    
Команда `put` добавляет документ в колллекцию. Синтаксис команды: `put <key> <value>`, где `<key>` - это расчитанный хеш от уникального поля документа или генерируемый псевдо-случайный хеш. `<value>` - данные в JSON формате. Например: 
    
    :::text
    kvlite_test1>
    kvlite_test1> put 14d86018060cfcdde7c7a462fca4acb62fbdb151 { 'key1': 'value1' }
    Done
    kvlite_test1> items
    14d86018060cfcdde7c7a462fca4acb62fbdb151
    {u'key1': u'value1'}
    kvlite_test1> 
    
Повторный вызов команды `put` с тем же самым ключом, но другими значениями, перезаписывает данные
    
    :::text
    kvlite_test1>
    kvlite_test1> put 14d86018060cfcdde7c7a462fca4acb62fbdb151 { 'key1': 'value1', 'key2': 'value2' }
    Done
    kvlite_test1> items
    14d86018060cfcdde7c7a462fca4acb62fbdb151
    {u'key1': u'value1', u'key2': u'value2'}
    kvlite_test1> 
    
Команда get выводит информацию  о документе по ключу 
    
    :::text
    kvlite_test1>
    kvlite_test1> get 14d86018060cfcdde7c7a462fca4acb62fbdb151
    14d86018060cfcdde7c7a462fca4acb62fbdb151
    {u'key1': u'value1', u'key2': u'value2'}
    kvlite_test1> 
    
Поддерживается передаче нескольких ключей 
    
    :::text
    kvlite_test1>
    kvlite_test1>get 14d86018060cfcdde7c7a462fca4acb62fbdb151 14d86018060cfcdde7c7a462fca4acb62fbdb152
    14d86018060cfcdde7c7a462fca4acb62fbdb151
    {u'key1': u'value1', u'key2': u'value2'}
    
    14d86018060cfcdde7c7a462fca4acb62fbdb152
    {u'key3': u'value3', u'key4': u'value4'}
    kvlite_test1>
     
Команд delete удаляет документ из коллекции. 
    
    :::text
    kvlite_test1>
    kvlite_test1>delete 14d86018060cfcdde7c7a462fca4acb62fbdb151
    Done
    kvlite_test1>items
    kvlite_test1>
    
