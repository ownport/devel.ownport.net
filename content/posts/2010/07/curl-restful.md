Title: cURL для тестирования RESTful приложений
Author: d.rey
Date: 2010-07-12 13:33:00
Slug: curl-restful
Tags: service,curl,rest,web

При разработке REST-приложений часто возникает необходимость в симуляции и тестировании отработки запросов с помощью различных HTTP методов. Для этих целей удобно использовать cURL [http://curl.haxx.se/](http://curl.haxx.se/). 

**GET**

    :::sh
    curl http://localhost:8080/item/key1/

**POST**

_application/x-www-form-urlencoded_ 

    :::sh
    curl -d “key1=value1” http://localhost:8080/items/

_multipart/form-data_ 

    :::sh
    curl -d “file=@d:\images\img0001.jpg” http://localhost:8080/images/

**PUT**

Передачу большого количества данных можно выполнить с помощью метода PUT. Для этого необходимо создать файл items: key1=value1&key2=values2&key3=value3 

    :::sh
    curl -T items http://localhost:8080/items/ 

**DELETE**

    :::sh
    curl -X DELETE http://localhost:8080/item/key1/

**Заметка**: с помощью флага -X можно выполнять все HTTP запросы: GET, POST, PUT, DELETE
