Title: Google авторизация на базе ClientLogin
Author: d.rey
Date: 2011-07-01 06:37:00
Slug: google-clientlogin
Tags: authorization,pyflowctrl,python,google,clientlogin

Когда приложению необходимо получить доступ к не публичным данным, расположенных в облаке Google, необходимо пройти авторизацию. Google API поддерживает протоколы:  OAuth 1.0,  OAuth 2.0, AuthSub или ClientLogin.  OAuth 2.0 является рекомендованным протоколом авторизации. В качестве примера, реализуем авторизацию на основании ClientLogin решения. Основная причина этому - простая реализация протокола и возможность использования уже наработанных решений по работе с HTTP методами. 

Одно из достоинств авторизации на базе ClientLogin - это возможность авторизации пользователей под их собственным аккаунтом. Выполнив авторизацию единожды и получив токен авторизации, можно создавать, читать, обновлять и удалять данные.

Для использования протокола ClientLogin необходима поддержка HTTP POST запросов. POST запрос должен быть структурирован как форма с кодированием данных application/x-www-form-urlencoded. Параметры должны быть включены в тело POST запроса. Описание параметров можно найти на странице [ClientLogin for Installed Applications](http://code.google.com/apis/accounts/docs/AuthForInstalledApps.html) и [Name of the Google service](http://code.google.com/apis/gdata/faq.html#clientlogin)

Пример кода, реализующего авторизацию с использованием библиотеки pyflowctrl 

    :::python
    import sys
    from processes.urlfetch2 import UrlFetch
    
    if len(sys.argv) <> 4:
        print 'usage: google-example2.py email password service'
        sys.exit()
    
    ClientLoginUrl  = 'https://www.google.com/accounts/ClientLogin'
    ClientLoginParams = {
        'accountType':  'GOOGLE',   
        'Email': sys.argv[1],    
        'Passwd': sys.argv[2],
        'service': sys.argv[3], 
    }
    
    urlfetch = UrlFetch()
    
    urlfetch.io['input'].put((ClientLoginUrl, 'POST', ClientLoginParams))
    urlfetch.run_once()
    (url, status_code, content, addinfo) = urlfetch.io['output'].get()
    if status_code == 200:
        auth = {}
        for auth_str in content.split('\n'):
            if auth_str:
                (k,v) = auth_str.split('=')
                auth[k] = v
        print auth
    else:
        print >> sys.stderr, 'Authentication failed', url, status_code, content, addinfo 
        
Из командной строки передается 3 параметра: email адрес (вида johndoe@gmail.com), пароль и название сервиса для которого нужно пройти авторизацию.  Основную задачу авторизации выполняет процесс [UrlFetch](http://devel.ownport.net/2011/06/http.html) 

    :::python
    urlfetch.io['input'].put((ClientLoginUrl, 'POST', ClientLoginParams))
    urlfetch.run_once()
    (url, status_code, content, addinfo) = urlfetch.io['output'].get()
    
После небольшого преобразования на выходе получаем авторизационный токен Auth в теле ответа, который можно в дальнейшем использовать для операций над данными 
    
    :::text
    SID=DQAAAL...QdNC3
    LSID=DQAAAL...N9XTZ
    Auth=DQAAAL...hWbU
    
Токен Auth можно использовать в дальнейшем для работы с данными

Авторизацию можно также пройти с помощью процесса [ClientLogin](https://code.google.com/p/sources-ownport/source/browse/pyflowctrl/processes/googleauth1.py)
    
    :::python
    >>> from processes.googleauth1 import ClientLogin
    >>> cl = ClientLogin()
    >>> cl.io['input'].put(('johndoe@gmail.com', 'johndoepwd', 'cp'))
    >>> cl.run_once()
    >>> cl.io['output'].get()
    ('DQAAA...cd6Q', 'DQAAA...2w7A', 'DQAAAK...ETw')
    
в качестве входных параметров для процесса ClientLogin передается список параметров: email (johndoe@gmail.com), пароль (johndoepwd) и название сервиса (cp - Contacts Data API). На выходе получаем список токенов (Auth, SID, LSID)

**Полезные ссылки**

- [Choosing an Auth Mechanism](http://code.google.com/apis/accounts/docs/GettingStarted.html)
- [Authentication and Authorization for Google APIs](http://code.google.com/apis/accounts/)
- [Using cURL to interact with Google Data services](http://code.google.com/apis/gdata/articles/using_cURL.html)

