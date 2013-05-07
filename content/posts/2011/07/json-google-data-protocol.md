Title: Использование JSON при работе с Google Data Protocol
Author: d.rey
Date: 2011-07-05 07:14:00
Slug: json-google-data-protocol
Tags: data,protocol,json,google

По умолчанию, для Google Data Protocol используется формат Atom. Если в запросе параметр alt не указан, то формат результата будет представлен именно в этом формате. Для того, чтобы получить результат в формате JSON необходимо указать это в запросе через параметр alt=json. Например, для получения данных календаря Google Developer в формате JSON запрос будет выглядеть следующим образом:

[http://www.google.com/calendar/feeds/developer-calendar@google.com/public/full?alt=json](http://www.google.com/calendar/feeds/developer-calendar@google.com/public/full?alt=json)

По умолчанию, Google Data сервисы выполняют преобразование JSON формата из XML согласно следующих правил:

- канал представляет собой JSON объект, в котором вложенные элементы или атрибуты представлены парой ключ/значение
- атрибуты конвертируются в тестовые строки
- наследуемые элементы конвертируются в объекты
- элементы, которые могут повторяться несколько раз, конвертируются в массивы
- текстовые значение тегов конвертируются в $t свойства
- если элемент содержит псевдоним пространства имен, псевдоним и элемент объединяются с помощью “$”. Например: ns:element преобразуется в  ns$element.
- атрибуты версии XML и атрибутов кодирования текста преобразуется в атрибуты version и encoding корневого элемента.

**Дополнительная информация о использовании JSON для работы с Google сервисами**

- [Using JSON in the Google Data Protocol](http://code.google.com/apis/gdata/docs/json.html)
- [Evolution of the Google Data Protocol](http://www.scribd.com/doc/16949574/Evolution-of-the-Google-Data-Protocol)
- [Google Data Protocol Reference](http://code.google.com/apis/gdata/docs/2.0/reference.html)
- [Working examples of how to use JSON with the Google Data APIs](http://code.google.com/apis/gdata/samples.html#JSON)

