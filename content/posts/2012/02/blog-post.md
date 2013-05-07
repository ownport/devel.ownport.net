Title: Описание сложных типов данных для потоков
Author: d.rey
Date: 2012-02-03 07:16:00
Slug: blog-post
Tags: structure,description,pyflowctrl,packet,json,scheme

Потоки играют важную роль в обмене информации между процессами. Единицей переноса информации в потоках являются [пакеты](http://devel.ownport.net/2012/01/pyflowctrl.html). Описание структур пакетов позволяется без анализа кода процесса связывать процессы между собой. Простые структуры данных (без вложений) содержащиеся в пакетах можно описать достаточно просто. Для каждого параметра определить имя, тип и описание. Для случаев, когда данные переданные в потоках имеют сложную,  иерархическую структуру для их описания требуется определенный синтаксис.

Древовидные структуры данных в python удобно представлять в виде словарей (dictionary). Можно провести параллель структуры словаря python с JSON форматом. В обоих случаях для хранения данных используются схожие подходы. Для описания данных хранимых в JSON формате используется [JSON Scheme](http://tools.ietf.org/html/draft-zyp-json-schema-03). Принципы заложенные в этой спецификации можно использовать и для описания данных, хранимых в python словарях, а соответственно использовать для описания структуры данных пакетов [pyflowctrl](http://code.google.com/p/pyflowctrl/).

Кроме возможности корректной связи между собой процессов, описание должно иметь форму доступную для анализа не только человеком, но и программой. Это позволит выполнять валидацию данных передаваемых между процессами.

Базовая спецификация JSON Schema определяет 8 типов данных JSON объекта: Object, Array, String, Number, Integer, Boolean, Null, Any. Два из них вложенные типы - Object и Array, 5 атомарных и один тип Any, который может содержать любые значения, включая Null. Для описания используются строки: “object”, “array”, “string”, “number”, “integerer”,”boolean”,”null”,”any”. Например:

    :::python
    "module": {
        "type": "string",
        "title": "Module name",
         "required": "true",
        }
        
где атрибут type - описывает тип, title - краткое описание свойства объекта. Дополнительно к title можно указать атрибут description для более детального документирования свойтсва объекта. Атрибуты title и description - опциональны. По умолчанию принято, что все свойства в схеме опциональные. Если какое-то свойство должно быть  обязательно определено, оно должно быть описано с помощью атрибута required.

Для описания структур, таких как массивы существует атрибут items, определяющий типы элементов в массиве.

    :::python
    "based_on": {
        "type": "array",
            "title": "list of bassed-on classes",
            "items": {
                "type": "string",
                "title": "Based-on class",
            }
    },
    
Для описания объектов используется атрибуты object и properties

    :::python
    {
        "type": "object",
        "title": "Class of module",
        "properties:": {
            "name": {
                "type": "string",
                "title": "name of class",
                "required": "true",
            },
            "path": {
                "type": "string",
                "title": "path for class importing",
                "required": "true",
            },
    }
    
Примеры, приведенные выше взяты из последней версии процесса [PyMetaInfo](http://code.google.com/p/pyflowctrl/source/browse/pyflowctrl/core4/processes/pymetainfo2.py)

**Ссылки:**

- JSON Scheme [http://tools.ietf.org/html/draft-zyp-json-schema-03](http://tools.ietf.org/html/draft-zyp-json-schema-03)
- JSON validator [http://code.google.com/p/jsonvalidator/](http://code.google.com/p/jsonvalidator/)

