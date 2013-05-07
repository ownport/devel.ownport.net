Title: Создание коллекции видео материалов GoogleDevelopers
Author: d.rey
Date: 2010-08-14 08:59:00
Slug: googledevelopers
Tags: reader,developers,python,google,rss,youtube

В предыдущих постах [Простой Google Reader API](http://devel.ownport.net/2010/08/google-reader-api.html) и [Простая реализация хранения данных на примере технологий FriendFeeds](http://devel.ownport.net/2010/08/friendfeed.html) рассказывалось о небольших библиотеках сбора и хранения данных. В данном посте оба эти подхода будут объединены с целью создания коллекции видео материалов презентаций [GoogleDevelopers](http://www.youtube.com/user/GoogleDevelopers).

Первое, это организовываем сбор данных. 

    :::python
    import sys
    import feedparser
    from time import sleep
    from simple.google.greader import get_auth_tokens, get_feed
    
    email='username@gmail.com'
    password='password'
    
    feed_url = 'http://gdata.youtube.com/feeds/base/users/GoogleDevelopers/uploads?alt=rss&v=2&orderby=published&client=ytapi-youtube-profile'
    
    tokens = get_auth_tokens(email,password)
    
    continuation = None
    try:
        while 1:
            rawfeed = get_feed(tokens['Auth'], feed_url, c=continuation)
            feed = feedparser.parse(rawfeed)
            
            for e in feed.entries:
                # here you can add your code for handling/storing entity
                pass
            try:
                continuation = feed['feed']['gr_continuation']
                sys.stderr.write('%s\n' % continuation)
            except:
                break
            sleep(10)
    except:
        sys.stderr.write('%s\n' % continuation)

Добавляем возможность сохранения данных в базе данных

    :::python
    ds = Datastore('mysql://<username>:<password>@localhost/<db>')
    if not ds.is_table_exist('youtube_googledevelopers'):
        ds.create_entity('youtube_googledevelopers')
    ...
            for e in feed.entries:
                ds.put('youtube_googledevelopers', 
                       dict(e), datahash_as_id=True)
            ds.commit()
    ...

P.S. Желательно обновить библиотеки [greader.py](http://sources-ownport.googlecode.com/hg/simple/google/greader.py) и [datastore.py](http://sources-ownport.googlecode.com/hg/parallel/libs/datastore.py), так как при написании данного поста в код было внесено несколько изменений:

- datastore.py: добавлена возможность создания id по контрольной сумме контента
- greader.py: исправлена проблема работы со сложными url. Таким к примеру является URL на RSS ленту [GoogleDevelopers](http://gdata.youtube.com/feeds/base/users/GoogleDevelopers/uploads?alt=rss&v=2&orderby=published&client=ytapi-youtube-profile)

