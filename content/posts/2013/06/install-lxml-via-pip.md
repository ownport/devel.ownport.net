Title: Заметки про установку lxml через pip
Author: d.rey
Date: 2013-06-21 08:35:00
Slug: install-lxml-via-pip
Tags: python, lxml, install, pip

Уставка python библиотеки [lxml](http://lxml.de/) через pip обычно не вызывает сложностей,
достаточно набрать в командной строке 

    :::sh
    pip install lxml
    
Но для случаев когда в системе не установлены все необходимые для lxml библиотеки, в ходе
установки могут появлятся ошибки:

    :::text
    gcc -pthread -fno-strict-aliasing -DNDEBUG -g -fwrapv -O2 -Wall -Wstrict-prototypes -fPIC 
    -I/usr/include/python2.7 -c src/lxml/lxml.etree.c -o build/temp.linux-x86_64-2.7/src/lxml/
    lxml.etree.o -w
    In file included from src/lxml/lxml.etree.c:239:0:
    src/lxml/etree_defs.h:9:31: fatal error: libxml/xmlversion.h: No such file or directory
    compilation terminated.
    error: command 'gcc' failed with exit status 1

В этом случае необходимо проверить, что установлены следующие библиотеки

    :::sh
    sudo apt-get install python-dev
    sudo apt-get install libxml2-dev
    sudo apt-get install libxslt1-dev

Если появится ошибка

    :::text
    gcc -pthread -shared -Wl,-O1 -Wl,-Bsymbolic-functions build/temp.linux-x86_64-2.5/src/
    lxml/lxml.etree.o -lxslt -lexslt -lxml2 -lz -lm -o build/lib.linux-x86_64-2.5/lxml/etree.so
    /usr/bin/ld: cannot find -lz
    collect2: ld returned 1 exit status
    error: command 'gcc' failed with exit status 1
    
В этом случае необходимо проверить, что установлена библиотека zlib1g-dev

    :::
    sudo apt-get install zlib1g-dev

