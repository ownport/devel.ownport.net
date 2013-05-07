Title: Простой Google Reader API
Author: d.rey
Date: 2010-08-13 12:27:00
Slug: google-reader-api
Tags: curl,reader,api,python,google

До настоящего времени Google Reader API официально не вышел, но это не останавливает разработчиков находить методы для работы с этим сервисом. Один из лучших ресурсов с описанием API для Google Reader находится по станице проекта [pyfeed](http://code.google.com/p/pyrfeed/wiki/GoogleReaderAPI). Этот документ наиболее полно описывает методы и принципы работы.


Для работы с Google Reader API необходимо иметь под рукой http(s) клиент, поддерживающий GET и POST запросы, cookie. Поддержка https протокола необходима для выполнения процедуры аутентификации. Поддержка cookie необходима  для доступа к ресурсам Google Reader. Но если вы можете добавлять значения в заголовок, то поддержка cookie опциональна.

Во многих примерах на официальном сайте [Google ](http://code.google.com/more/)для работы с сервисами, используется утилита [curl](http://curl.haxx.se/), достаточно простая в использовании, но в то же время и достаточно функциональная. Для упрощения работы с curl при работе с Google Reader, я написал две простые обвертки на python: одна, облегчает аутентификацию, вторая, упрощает доступ к элементам постов.

Библиотека с этими функциями доступна в репозитории [sources-ownport](http://sources-ownport.googlecode.com/hg/simple/google/greader.py). Остановлюсь на этих функция несколько подробнее:

**get_auth_tokens(email, password)**

в качестве входных параметров передается два значения: email адрес и пароль. Функция возвращает список значений: SID, LSID, Auth. Из перечисленных токенов нам будет необходим только Auth, который используется в случае Google Reader для аутентификации и будет в дальнейшем использован в функции get_feed

**get_feed(auth_token, feed_url, \*\*kargs)**

про auth_token  я уже рассказал, это результат выполнения функции get_auth_token. Входной параметр feed_url содержит URL на RSS ленту сайта. Например, для этого сайта feed_url будет вид [http://devel.ownport.net/feeds/posts/default](http://devel.ownport.net/feeds/posts/default). 

На некоторых переменных из kargs, как наиболее чаще используемых, остановлюсь подробней:

- `n`, описывает число возвращаемых элементов RSS ленты. Значение по умолчанию, 20.
- `r`,  описывает в какой последовательности будут выдаваться данные. Назад в прошлое (значение по умолчанию, d) или в обратной последовательности (значение о).
- `ot`,  время в unix формате (число секунд с первого января 1970 года, 00:00 UTC) начиная с которого выдавать данные
- `c`, строка используется для продолжения получения данных. Каждый раз когда делается запрос, скорее всего не все элементы RSS ленты будут возвращены, а только их часть. В возвращенном ответе находится параметр _gr:continuation_, добавив который в качестве аргумента в запросе, можно получить следующую порцию данных.

Функция возвращает RSS ленту.

В следующем посте я приведу пример использования данных функций на примере получения видео материалов [Google Developers размещенных на YouTube](http://www.youtube.com/user/GoogleDevelopers)

**Дополнительные ссылки:**

- [http://code.google.com/p/pyrfeed/wiki/GoogleReaderAPI](http://code.google.com/p/pyrfeed/wiki/GoogleReaderAPI)
- [http://code.google.com/apis/gdata/articles/using_cURL.html](http://code.google.com/apis/gdata/articles/using_cURL.html)
- [http://timbroder.com/2007/08/google-reader-api-functions.html](http://timbroder.com/2007/08/google-reader-api-functions.html)
- [http://www.niallkennedy.com/blog/2005/12/google-reader-api.html](http://www.niallkennedy.com/blog/2005/12/google-reader-api.html)

