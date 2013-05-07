Title: Форма аутентификации в центре экрана
Author: d.rey
Date: 2010-01-01 21:12:00
Slug: blog-post
Tags: stylesheet,html,css

Довольно часто возникает необходимость организации доступа по паролю к различным ресурсам сайта. Основная идея этого поста, рассмотреть возможность расположения формы аутентификации посередине экрана вне зависимости от его размера.

Внешний вид формы:

![](http://1.bp.blogspot.com/_XzhxWqanLlk/Sz5Jcz952QI/AAAAAAAAADI/rmceSG8CvvY/s320/login_form.PNG)

Код формы:

    :::html
    <div id='auth_login_form'>
        <form action='' method='POST'>
            <h3>Authentication required</h3>
            <div id='error_msg'/>
            <p>Please enter your username/password</p>
            Username: <input type='text' name='username' size='20'/><br/>
            Password: <input type='password' name='password' size='20'/><br/>
            <input type='submit' value='Enter'/>
        </form>
    </div> 

Воспользуемся возможностями CSS и добавим в таблицу стилей описание блока auth_login_form: 

    :::css
    #auth_login_form {
    position: absolute;
    top: 50%;
    left: 50%;
    margin-left: -150px;
    margin-top: -90px;
    width: 300px;
    height: 180px;
    
    text-align: center;
    font-family: Tahoma, Verdana, Arial, sans-serif;
    font-size: 12px;
    
    border: 1px solid;
    /* experiments only for Firefox and Safari/Webkit */
    border-radius: 7px;
    -webkit-box-shadow: #888 3px 3px 3px;
    }

Основную работу по расположению формы в центре экрана выполняют _position, top, left, margin-left, margin-top, width, heigth_ переменные. Расположением текста и элементов, а также параметры шрифтов внутри формы управляются _text-align, font-family, font-size_.

На что хотелось бы обратить внимание, так это на возможности _border-radius_ и _-webkit-box-shadow_.

_border-radius_: позволяет округлить углы в блоках, указывая радиус округления. Раньше для этого нужно было использовать изображения, которые размещались в углах формы и создавали видимость округления. _border-radius_ поддерживается в Firefox, Safari/Webkit.

_-webkit-box-shadow_: еще одна интересная возможность это добавление тени к блоку. Можно управлять цветом и размером тени. Поддерживается в Safari/Webkit.

Получить более детальную информацию об этих переменных можно на сайте CSS3 [http://www.css3.info/preview/](http://www.css3.info/preview/). 

Еще один вариант решения, это использование таблиц:

    :::html
    <body>
        <table width="100%" height="100%"> 
            <tr> <td align="center" valign="middle">The text is in center of screen.</td> </tr> 
        </table> 
    </body>
    
