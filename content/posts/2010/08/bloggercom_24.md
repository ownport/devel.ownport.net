Title: Похожие посты на blogger.com
Author: d.rey
Date: 2010-08-24 06:25:00
Slug: bloggercom_24
Tags: javascript,blogger,api,jquery,widget,google

Несколько дней назад подключил автоматическое добавление похожих постов для своего блога. Но просто добавить виджет/гаджет - это не очень интересно. Интересно разобраться, как он работает. Мне очень повезло, что [Майк, автор виджета](http://www.moretechtips.net/), не просто выложил его в свободный доступ, но еще написал несколько постов о том, как установить, настроить виджет, а также о том, как он работает. Для меня, как для человека, имеющего лишь общее представление о JavaScript, разобраться в работе виджета, является чуть ли не идеальным способом в изучении нового языка..

В основе принципов работы виджета лежит работа с [Google Data API](http://code.google.com/apis/gdata/http://code.google.com/apis/gdata/). API предлагает простой протокол для чтения содержимого блога в формате feed. Если учесть, что большинство сервисов Google поддерживают Google Data API протокол, умение с ним работать - крайне полезное знание. Для работы с Blogger существует [специальный набор API](http://code.google.com/apis/gdata/docs/2.0/reference.html), позволяющий выполнять запросы к blogger.com и получать ответы в JSON формате.

Для обработки результатов в скрипте используется библиотека [jQuery](http://jquery.com/), с помощью которой выполняется выборка элементов, запрос постов, обработка результатов, производится сравнение тегов для поиска близких по тематике постов.

Для начала работы необходима JavaScript библиотека related-posts-widget-1.0.js. Ее оригинал доступен на [http://code.google.com/p/blogger-related-posts/](http://code.google.com/p/blogger-related-posts/). Моя версия скрипта доступна в репозитории на [sources-ownport](http://sources-ownport.googlecode.com/files/related-posts-widget-1.0.js). Именно над ней я собственно и проводил эксперименты.

**Как работает этот виджет?** 

В начале вызывается класс relatedPostsWidget. Конструктор класса определяет параметры по-умолчанию. Используя  jQuery метод extend  переопределяются значения параметров пользовательскими значениями. 

    :::html
    <script type="text/javascript">
        relatedPostsWidget({'containerSelector':'div.post-body', 
            'loadingText':'загрузка похожих постов...',
            'recentTitle':'Поcледние посты',
            'relatedTitle':'Похожие посты'}); 
    </script>

Затем осуществляется проверка: если  селектор не определен, добавляется div c идентификатором related-posts в месте выполнения скрипта. Он будет необходим в будущем для вывода результатов. 

    :::javascript
    if (!op.containerSelector) {
        document.write('<div id="related-posts"></div>');
        op.containerSelector = '#related-posts';
    };

Функция инициализации выполняется как только документ будет загружен и готов 

    :::javascript
    // Call init on document ready
    if(op.onLoad) $(window).load(initRelatedPosts);
    else $(document).ready(initRelatedPosts);

Решение о том, когда выводить список похожих постов на странице принимается на основании, количестве селекторов на странице. 

    :::javascript
    // append my div to user selected container 
    if (op.containerSelector != '#related-posts'){
        var container = $(op.containerSelector);
        // check contianer is there and only one for pages like home
        if (container.length!=1) return;
            div = $('<div id="related-posts"></div>').appendTo(container);
    }
    else div = $(op.containerSelector); // div which I wrote on document 

На основании результатов поиска и выборки всех ссылок с аттрибутом rel=’tag’ формируется список тегов для поста.

    :::javascript
    // get tags if wasn't preset
    if (!op.tags) {
        op.tags = [];
        $('a[rel="tag"]:lt('+op.maxTags+')').each(function () {
            var tag= $.trim($(this).text().replace(/\n/g,''));
            if($.inArray(tag,op.tags)==-1) op.tags[op.tags.length]=tag;
        });
    };

После оформления заголовка “Последние посты” или “Похожие посты”, а также строки состояния “Загрузка” (эти значения можно изменить с помощью переменных recentTitle, relatedTitle, loadingText соответственно) выполняется запрос на получение последних или похожих постов. Условия выбора осуществляется на основании того, определены теги в посте или нет 

    :::javascript
    //recent posts
    if(op.tags.length==0){
        ...
        // Tags found , do related posts widget
    }else{
        // Requesting json feeds for each tag    
        ...
    };

Если теги не найдены, выполняется запрос на получение последних постов через Google Data API 

    :::javascript
    $.ajax({url:op.blogURL+'/feeds/posts/summary/'
        ,data:{'max-results':op.maxPostsPerTag,'alt':'json-in-script'}
        ,success:tagLoaded
        ,dataType:'jsonp'
        ,cache:true });

Для случая, когда теги определены,  выполняется расчет и получение списка похожих постов. 

    :::javascript
    for(var t=0; t<op.tags.length;t++) 
        $.ajax({url:op.blogURL+'/feeds/posts/summary/',data:{'category':op.tags[t],'max-results':op.maxPostsPerTag,'alt':'json-in-script'}
        ,success:tagLoaded
        ,dataType:'jsonp'
        ,cache:true });

Для каждого тега выполняется запрос постов, содержащих данный тег, а затем выполняется расчет списка постов на основании числа общих тегов: функции tagLoaded и addPost.

Для управления стилями можно воспользоваться следующей заготовкой и изменить ее согласно стилю и оформлению блога

    :::css
    <style type="text/css">
       /* Widget Div */
       #related-posts{
          /* Add your styles */
       }
       /* Widget H2 heading */
       #related-posts h2{
          /* Add your styles */
       }
       /* Widget UL list */
       #related-posts ul{
          /* Add your styles */
       }
       /* Widget loading text */
       #related-posts-loadingtext{
          color:green;
       }
       /* Widget loading class set on UL until it is loaded */
       /* you will need set the option: 'loadingClass':'related-posts-loading' */
       .related-posts-loading {
          /* Ajax Icon as background */
          background:transparent url(Load.gif) no-repeat right top;
       }
       /* Style links based on relevacny */
       /* you will need set the option: 'postScoreClass':'related-post' */
       .related-post1{ font-size:90%}
       .related-post2{ font-size:90%}
       .related-post3{ font-size:100%}
       .related-post4{ font-size:110%}
       .related-post5{ font-size:120%}
    </style>

При написании поста использованы материалы:

- [http://www.moretechtips.net/2009/04/code-behind-smarter-related-posts.html](http://www.moretechtips.net/2009/04/code-behind-smarter-related-posts.html)
- [http://www.moretechtips.net/2009/06/rebuilding-google-blog-bar-jquery-ed.html](http://www.moretechtips.net/2009/06/rebuilding-google-blog-bar-jquery-ed.html)
