Title: Эволюция веб приложений в диаграммах
Author: d.rey
Date: 2010-04-22 11:07:00
Slug: blog-post_22
Tags: application,cloud,development,distribution,web,computing

С каждым годом все что мы знали или знаем меняется. Ничто не стоит на месте. Кого-то это расстраивает, кого наоборот радует. В любом случае изменения происходят и это нужно принять как естественное. Так и с веб приложениями. 

Когда-то веб приложения (если их можно так назвать) представляли собой набор статических html страниц

![](http://3.bp.blogspot.com/_XzhxWqanLlk/S9AB0t82s2I/AAAAAAAAAIA/FPD3LQDMjdM/s400/static_pages.png)

Со временем появилась необходимость в динамике и была добавлена возможность использования скриптов (CGI) для генерации страниц и доступа к базам данных

![](http://2.bp.blogspot.com/_XzhxWqanLlk/S9AB_36ALbI/AAAAAAAAAII/tyankOxWa5s/s400/scripts.png)

Все серверные компоненты находились на одном хосте

![](http://3.bp.blogspot.com/_XzhxWqanLlk/S9ACFFVASsI/AAAAAAAAAIQ/G12z-vZ-O0I/s200/one_host.png)

С ростом нагрузки и в зависимости от ее природы элементы веб приложений стали разносить по разным хостам, объединять в кластеры для увеличения производительности.

![](http://1.bp.blogspot.com/_XzhxWqanLlk/S9ACSrKg_dI/AAAAAAAAAIY/BPZJgsgFSY4/s320/distributed_db.png)

![](http://3.bp.blogspot.com/_XzhxWqanLlk/S9ACZFbk2oI/AAAAAAAAAIg/daK0WdECg_Q/s320/distributed_web_applications.png)

![](http://3.bp.blogspot.com/_XzhxWqanLlk/S9ACc-atFcI/AAAAAAAAAIo/Pd4jwk8gvRg/s200/all_distributed.png)

Последнее время все больше становится популярным использование облачных технологий ([cloud computing](http://en.wikipedia.org/wiki/Cloud_computing)) предоставляющих различные концепции: [все как услуга](http://ru.wikipedia.org/w/index.php?title=Everything_as_a_service), [инфраструктура как услуга](http://ru.wikipedia.org/wiki/Infrastructure_as_a_service), [платформа как услуга](http://ru.wikipedia.org/wiki/Platform_as_a_service), [программное обеспечение как услуга](http://ru.wikipedia.org/wiki/Software_as_a_service), рабочее место как услуга ([Workspace as service](http://ru.wikipedia.org/wiki/Workplace_as_a_service), [Desktops as service](http://ru.wikipedia.org/wiki/Desktops_as_a_service)). Данные услуги призваны упростить или правильнее сказать сократить расходы связанные с обслуживанием аппаратного и программного обеспечения, сконцентрировавшись больше на создании/развитии продукта. 

В каком направлении будут эволюционировать приложения далее, в будущем? Посмотрим.
