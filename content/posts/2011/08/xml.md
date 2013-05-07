Title: Преобразование xml в словарь
Author: d.rey
Date: 2011-08-21 21:21:00
Slug: xml
Tags: pyflowctrl,xml,python,dictionary

Несколько дней назад мне потребовалось обработать несколько тысяч небольших (от нескольких килобайт до 3 Мб) xml файлов, содержащих различные статистические данные.  В чистом виде работать с xml файлами не очень удобно. Гораздо проще, когда сложные структуры такие как данные в xml файлах представлены в виде словаря. Процесс [xml2dict](http://code.google.com/p/sources-ownport/source/browse/pyflowctrl/processes/xml2dict1.py) предназначен как раз для таких задач.

На вход процесса input подаются данные в виде списка из имени xml файла и его содержимого. На выходе получаем список состоящий из имени файла и словаря, данные которого соответствуют входному xml файлу. Парсинг исходных данных выполняется с помощью входящей в стандартный пакет python библиотеки [cElementTree](http://docs.python.org/library/xml.etree.elementtree.html). В связи с тем, что для обработки используется DOM подход, данные XML файла загружаются в память целиком. Нужно быть аккуратным при работе с большими объемами данных.

Пример исходных данных (xml_data): 

    :::xml
    <?xml version="1.0"?>
    <MeasurementData>
      <Setup  startTime="2011-06-24T02:45:00.000-04:00:00" interval="15">
        <Result>
          <Object>
            	<DN><![CDATA[platform1]]></DN>
          </Object>
          <Target measurementType="CPULoad">
            <cpuAverageLoad>65</cpuAverageLoad>
            <cpuPeakLoad>87</cpuPeakLoad>
            <memFree>344985</memFree>
            <memUsed>1456239</memUsed>
            <sysAvailability>54</sysAvailability>
            <sysUptime>569</sysUptime>
          </Target>
        </Result>
      </Setup>
    </MeasurementData>
Пример использования процесса xml2dict 

    :::python
    import pprint
    from processes.xml2dict1 import xml2dict
    x2d = xml2dict()
    pprint.pprint(x2d.run_once(data=(None, xml_data)))
    
На выходе процесса получаем следующий словарь 

    :::python
    (None, {'MeasurementData': {'Setup': {'@interval': '15',
                                    '@startTime': '2011-06-24T02:45:00.000-04:00:00',
                                    'Result': {'Object': {'DN': {'$': 'platform1'}},
                                               'Target': {'@measurementType': 'CPULoad',
                                                          'cpuAverageLoad': {'$': '65'},
                                                          'cpuPeakLoad': {'$': '87'},
                                                          'memFree': {'$': '344985'},
                                                          'memUsed': {'$': '1456239'},
                                                          'sysAvailability': {'$': '54'},
                                                          'sysUptime': {'$': '569'}}}}}})
                                                          
В некоторых xml файлах используются пространства имен (namespace). По умолчанию в процессе xml2dict они удаляются. Для случаев, когда пространства имен необходимы, отключить их удаление можно с помощью параметра remove_xmlns: 

    :::python
    x2d = xml2dict(remove_xmlns=False)

Процесс [xml2dict](http://code.google.com/p/sources-ownport/source/browse/pyflowctrl/processes/xml2dict1.py)

P.S. Небольшой юбилей - 100й пост

