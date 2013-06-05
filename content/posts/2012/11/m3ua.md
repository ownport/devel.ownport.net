Title: Анализ M3UA трейсов
Author: d.rey
Date: 2012-11-16 09:03:00
Slug: m3ua
Tags: text2pcap,python,telecom,mtp3,wireshark,m3ua,tshark

Переход на IP транспорт в телекоме позволил во многом упростить сбор и анализ сигнальных сообщений между сетевыми элементами. Сейчас для это не нужно покупать дорогостоящее оборудование. Практический каждый cisco коммутатор или маршрутизатор позволяет сделать mirroring пакетов с необходимых интерфейсов на специально выделенный порт к которому подключен компьютер или лептоп с [wireshark](http://www.wireshark.org). 

К сожалению, не все так радужно. При большой нагрузке, несколько сообщений выше SCTP уровня могут упаковываться в один IP пакет. Как следствие, анализ сообщений затрудняется тем, что в одном пакете содержится информация о нескольких событиях. Так например, в одном IP пакете может быть информация и ISUP, и MAP, и CAMEL протоколов. Выполнив выборку скажем по паре transaction id, можно получить результат содержащий лишь 20-30% полезной информации, а все остальное - это данные, относящиеся к другим событиям.

Данная проблема стала причиной написания небольшого скрипта на python - [m3ua-unbundle](https://github.com/ownport/m3ua-unbundle), который позволяет выполнить выборку SCTP/M3UA сообщений, преобразовать эти сообщения в MTP3 и сохранить каждое сообщение в отдельном пакете. На выходе получается pcap файл, который можно легко анализировать в wireshark. Если вернуться к примеру выше, то поиск сообщений по паре transaction id позволит получить только сообщения относящиеся к только этой транзакции. 

В качестве бонуса, преобразование SCTP/M3UA в MTP3 позволяет сократить размер трейса практически в 2 раза.