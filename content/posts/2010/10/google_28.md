Title: Google: как обрабатываются поисковые запросы
Author: d.rey
Date: 2010-10-28 10:07:00
Slug: google_28
Tags: overview,search,google

Этот пост является продолжением предыдущего поста [Архитектура кластеров Google](http://devel.ownport.net/2010/10/google.html) и раскрывает принципы лежащие в основе обработки поисковых запросов пользователя. Каковы задачи Google Web Server, что такое индексные сервера и сервера документов? Какова их роль в обработке запросов? Каким образом Google обрабатывает тысячи запросов в секунду, поступающие от пользователей со всего мира? Каковы архитектурные решения используются для того, чтобы выдерживать такую нагрузку?

Когда пользователь выполняет поиск, броузер отсылает HTTP запрос на один из кластеров Google. В дальнейшем вся обработка запроса осуществляться внутри кластера. Балансировщик нагрузки перенаправляет запрос на один из веб-серверов (Google Web Server, GWS). GWS играет главную роль в обработке запроса пользователя:

- отправляет запрос на проверку орфографии слов, используемых в запросе пользователя (spell-checking system)
- отправляет запрос на системы формирования рекламных блоков (ad-serving system)
- контролирует фазы поиска
- отвечает за формирование результатов

![](http://2.bp.blogspot.com/_XzhxWqanLlk/TMkeIzjRX5I/AAAAAAAAASs/W_MYwR5T9dw/s1600/Google+query-serving+architecture.PNG)

Поиск достаточно сложный процесс. Исходные данные (документы) могут занимать десяток, сотни терабайт, если не петабайты. Размеры индексов для этих данных занимают не меньше, это терабайты данных. Работа с такими объемами данных требует больших вычислительных мощностей.  К счастью поиск по индексам легко поддается распараллеливанию. Индекс разбивается на части и распределяется между узлами кластера. Каждый узел отвечает за поиск по данным, находящимся локально на дисках. Тоже самое происходит и с документами. В зависимости от того, какие данные (индекс или документы) хранит узел и характеризует, какую роль он будет выполнять - индексный сервер или сервер документов.

Выполнение запроса делится на две фазы. В первой фазе индексные сервера для каждого слова в запросе формируют список идентификаторов документов. Результирующий список документов определяется путем выделения общих идентификаторов документов для каждого слова и проставления весов (оценка актуальности) для каждого документа. Величина веса документа определяет порядок, в котором документы будут представлены в результате. 

Во второй фазе, на основании списка идентификаторов документов, сервера документов выдают информацию о названии документа, ссылки на него (URL), краткого описания документа, а также фрагменты текста, содержащие ключевые слова поиска.

Результирующие данные о документах возвращаются на GWS, где и формируется окончательное представление искомой информации в формате HTML.

*Примечание*: В посте изложено лишь мое понимание работы системы на основании статей, брошюр, найденных в Интернет. Если у вас есть замечания, поправки или исправления - с радостью их выслушаю в комментариях.
