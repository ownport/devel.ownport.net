Title: Расширенный urlfetch
Author: d.rey
Date: 2012-03-04 09:38:00
Slug: urlfecth
Tags: download,url,fetch,python,html

О способах получения данных из интернет уже упоминалось несколько раз. Предыдущие примеры позволяли на базе простых функций, компонент или процессов загружать тестовую информации из интернет-ресурсов. Некоторые из них, позволяли просто загружать данные, другие выполнять загрузку последовательно на основании списка, некоторые параллельно. За время использования предыдущих версий стало понятно, что только отправка HTTP GET и получения данных не всегда является достаточным, а некоторых случаях просто не эффективно. Необходим подход позволяющий более эффективно осуществлять запросы с серверам и обрабатывать полученные данные. Решение пришло неожиданно и как это всегда бывает - лежало перед носом. [Universal Feed Parser](http://code.google.com/p/feedparser/) - это отличный модуль, написанный на python, позволяющий загружать и обрабатывать RSS ленты различных форматов.

Модуль [urlfetch](http://code.google.com/p/sources-ownport/wiki/UrlFetch) является пеработкой feedparser-а. Основной акцент делается на загрузку текстовых данных, поэтому код специфичный для работы с RSS/Atom лентами был убран. Из-за этого упростились многие функции, например, такие как определение кодировки данных на основании содержимого. Как и feedparser, так и urlfetch достаточно легок в использовании. Модуль не зависим от сторонних библиотек, поэтому может работать на базовом python версии 2.4 и выше (на python 3 не тестировался). Для экспериментов  включена поддержка работы из командной строки:

    :::text
    #python urlfetch.py <URL>

Результатом выполнения будет вывод на экран словаря, содержащего две секции - полей заголовка (headers) и собственно само содержимое страницы (body). Для интеграции urlfetch в приложение используется единственная функция urlfetch.fetch() 

    :::python
    def fetch(url, etag=None, modified=None, agent=None, referrer=None, handlers=[], request_headers={}, absolute_urls=False)
    
которая возвращает словарь, состоящий из заголовка и содержимого страницы. Описание и примеры использования приведены на wiki странице модуля [urlfetch](http://code.google.com/p/sources-ownport/wiki/UrlFetch), поэтому ниже опишу лишь возможности реализованные в текущей версии:

- поддержка [ETag](http://en.wikipedia.org/wiki/HTTP_ETag), позволяет использовать один из механизмов работы с кешем web страниц на стороне сервера, сохраняя пропускную способность и ресурсы сервера. Клиент посылая запрос с значением ETag поля, полученным в прошлом запросе, дает возможность серверу оценить необходимость отправки запрашиваемой информации повторно. Если данные на стороне сервера не изменились  с последнего обращения, сервер возвращает ответ с [HTTP 304](http://en.wikipedia.org/wiki/HTTP_304#3xx_Redirection) &nbsp;без передачи самого содержимого ресурса - указывая на то, что данные не изменены.
- поддержка [Last-Modified](http://en.wikipedia.org/wiki/List_of_HTTP_header_fields). Это второй механизм, поддерживаемый urlfetch для работы с кеширующими серверами. В отличии от ETag, передается дата и время последнего изменения ресурса.
- поддержка User-Agent. По умолчанию urlfetch передает строку “UrlFetch/0.1 +http://code.google.com/p/sources-ownport”. Если вы встраиваете urlfetch в свое приложение, вы должны изменить значение User-Agent на свое, согласно правилам [http://en.wikipedia.org/wiki/User_agent](http://en.wikipedia.org/wiki/User_agent)
- поддержка [HTTP Referrer](http://en.wikipedia.org/wiki/HTTP_referrer), адрес web страницы с которой осуществлено обращение к запрашиваемой странице.
- поддержка пользовательских обработчиков используемых в [urllib2.build_opener](http://docs.python.org/library/urllib2.html#urllib2.build_opener)
- поддержка переопределения значений по умолчанию в HTTP запросах на пользовательские
- автоматическое преобразование содержимого станицы в UTF-8 кодировку на основании информации переданной сервером в HTTP заголовке (content-type, charset)
- возможность преобразование относительных ссылок внутри содержимого страницы на абсолютные, например ссылка “/2012/02/mysql.html” будет заменена на “http://devel.ownport.net/2012/02/mysql.html”

Исходный код модуля urlfetch доступен на [http://code.google.com/p/sources-ownport/](http://code.google.com/p/sources-ownport/source/browse/urlfetch). [Wiki страница с описанием работы](http://code.google.com/p/sources-ownport/wiki/UrlFetch).