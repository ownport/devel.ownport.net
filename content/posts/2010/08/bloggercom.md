Title: Blogger.com: как убрать панель навигации
Author: d.rey
Date: 2010-08-22 07:01:00
Slug: bloggercom
Tags: blogger,hint,advice

Для того, чтобы скрыть панель навигации для вашего блога достаточно зайти в Dashboard/Design/Edit HTML и в Edit Template добавить CSS определение для панели: 

    :::css
    #navbar-iframe {
       display: none !important;
    }
    
Удаление этого кода, вернет панель обратно
