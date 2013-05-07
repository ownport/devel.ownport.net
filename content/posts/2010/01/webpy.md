Title: Аутентификация в web.py
Author: d.rey
Date: 2010-01-12 10:19:00
Slug: webpy
Tags: python,authentication,web.py

Довольно редко можно встретить сайт в интернете у которого нет возможности регистрации пользователей. Форумы, блоги, интернет-магазины, социальные сети обязуют или рекомендуют своим пользователям пройти процедуру регистрации (аутентификации). 

Рассмотрим один из способов реализации аутентификации на базе фреймворка web.py с помощью базового модуля session.py. 

Для доступа к ресурсам сайта используются следующие URI:

- _/_ или любые другие ссылки с помощью которых пользователь получает доступ к ресурсам сайта. Для упрощения рассмотрим только '/'.
- _/login_ - отображает форму аутентификации
- _/logout_ - закрывает сессию пользователя и переходит к форме аутентификации 

Пример на python:

    :::python
    urls = (
        '/', 'index',
        '/login', 'libs.auth.login',
        '/logout', 'libs.auth.logout',
    )

Для того чтобы при каждом обращении '/' происходила проверка статуса сессии задекорируем функцию index() декоратором _@login_required_.

    :::python
    class index:
        @login_required
        def GET(self):
            ...

Каждый раз при обращении по GET запросу экземпляра класса index будет вызываться функция _login_required_:

    :::python
    def login_required(func):
        def decorator(*args, **kargs):
            try:
                if web.config._sessions.logged_in:
                    return func(*args, **kargs)
                else:
                    raise SessionExpired()
            except AttributeError:
                raise SessionExpired()
            return func(*args, **kargs)
        return decorator

Эта функция делает проверку был ли аутентифицирован пользователь или нет (_web.config._session.logged_in_ и _AttributeError_). Если нет, вызывается исключение _SessionExpired _и произойдет автоматическая переадресация на форму аутентификации '_/login_'. Класс _SessionExpired _является базовым и входит в состав модуля _session.py_. Для того, чтобы выполнить переадресацию этот класс нужно переопределить.

    :::python
    class SessionExpired(web.HTTPError): 
        def __init__(self, *args, **kargs):
            web.HTTPError.__init__(self, '303 See Other', {'Location': '/login'})

Далее, сразу после подключения обработки сессий, базовый _SessionExpired_ нужно подменить на новый:

    :::python
    if __name__ == "__main__":
        app = web.application(urls, globals())
        web.config._sessions = web.session.Session(app, DBStore('sessions'), initializer={})
        web.session.SessionExpired = SessionExpired
        app.run()

Остался последний не раскрытый вопрос - обработка процедуры аутентификации пользователя.  Пользователь попадая на форму аутентификации заполняет ее (поля: имя пользователя и пароль) и отправляет данные на сервер.  Форма аутентификации выглядит достаточно просто. Можно воспользоваться примером, приведенным в посте [Форма аутентификации в центре экрана](http://devel.ownport.net/2010/01/blog-post.html) 

Сервер получает данные формы в запросе POST:

    :::python
    class login:
        def GET(self):
            return login_form()
    
    def POST(self):
        from hashlib import md5
    
        username = web.input().username
        password = md5(web.input().password).hexdigest()
        users =  db.select('users',  limit=1, where="username=$username AND password = $password", vars=locals())
        try:
            user = users[0]
            web.config._sessions.username = user.username
            web.config._sessions.privilege = user.privilege
            web.config._sessions.logged_in = True
        except IndexError:    
            return login_form(username = username, error_msg='Unknown username or wrong password')
        raise web.session.SessionExpired()

В методе POST осуществляется проверка имени пользователя и пароля с данными хранящимися в базе данных. Если данная проверка успешна, данные пользователя сохраняются в переменных сессии для данного пользователя.

О аутентификации более подробно можно узнать на wikipedia [http://ru.wikipedia.org/wiki/Аутентификация](http://ru.wikipedia.org/wiki/%D0%90%D1%83%D1%82%D0%B5%D0%BD%D1%82%D0%B8%D1%84%D0%B8%D0%BA%D0%B0%D1%86%D0%B8%D1%8F)

**О декораторах**

- [http://devel.ownport.net/2010/01/python-2.html](http://devel.ownport.net/2010/01/python-2.html)
- [http://devel.ownport.net/2010/01/python.html](http://devel.ownport.net/2010/01/python.html)

**Дополнительная информация и примеры других решений**

- [http://jpscaletti.com/webpy_auth/](http://jpscaletti.com/webpy_auth/)
- [http://webpy.infogami.com/authentication](http://webpy.infogami.com/authentication)
- [http://dumpz.org/356/](http://dumpz.org/356/)

