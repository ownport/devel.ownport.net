Title: Инсталляция Ubuntu 11.04 с сетевой консоли
Author: d.rey
Date: 2011-05-16 23:21:00
Slug: ubuntu-1104
Tags: install,network,linux,ubuntu,console

![](https://lh4.googleusercontent.com/_XzhxWqanLlk/TdGGh91j7fI/AAAAAAAAAUY/RgNYxg8tKqU/Ubuntu-11.04-Release.jpg)

Около недели назад мне понадобилось установить Ubuntu http://www.ubuntu.com на один из серверов. К сожалению монитора под рукой не оказалось. Можно было бы достать монитор в офисе, но спортивный интерес возможности установки системы без монитора и клавиатуры взял верх. Кто знает, возможно  это может понадобится в будущем. Описанное решение позволяет выполнить установку ubuntu только с помощью одной флешки. Конечно же при условии, что в BIOS уже установлена загрузка с USB.

Специалисты по безопасности конечно же возразят, что разрешение загрузки системы с USB - это серьезное нарушение безопасности. С этим сложно не согласиться, поэтому вопросы безопасности опустим.

Для подготовки инсталяционного образа понадобится:

- флешка емкость не менее 32 Мбайт
- операционная система linux, в моем случае я использовал [Ubuntu 11.04](http://www.ubuntu.com)
- приложение [ISO Master](http://www.littlesvr.ca/isomaster/)
- [минимальные инсталляционные образы Ubuntu](https://help.ubuntu.com/community/Installation/MinimalCD)

Я покажу на примере подготовки образа для платформы i386. Думаю, что идея будет практически одинакова для всех платформ. Для x86_64 так точно. Про PowerPC, Itanium, Sparc сложно сказать, никогда не пробовал.

[Загруженный образ](http://archive.ubuntu.com/ubuntu/dists/natty/main/installer-i386/current/images/netboot/mini.iso) необходимо открыть в ISO Master и удалить все файлы кроме: boot.cat, initrd.gz, isolinux.bin, linux, syslinux.cfg

Для активации сетевой консоли для начала необходимо извлечь файл initrd.gz и распаковать его во временную директорию.

    :::sh
    # mkdir initrd.i386
    # cd initrd.i386
    # gzip -d < ../initrd.gz | cpio --extract --verbose --make-directories --no-absolute-filenames 
    
В корне директории initrd.i386 необходимо создать файл preseed.cfg:  _# nano -w preseed.cfg _  cо следующим содержимым:  

    :::text
    ### activate network console 
    d-i anna/choose_modules string network-console 
    d-i network-console/password password r00tme 
    d-i network-console/password-again password r00tme 
    
Создание нового initrd.gz и удаление временной директории: 

    :::sh
    # find . | cpio -H newc --create --verbose | gzip -9 > ../initrd.gz
    # cd ../
    # rm -fr initrd.i386/ 
    
С помощью ISO Master новый initrd.gz переписывается на место старого. Заключительным шагом является изменение syslinux.cfg: 
    
    :::text
    default console
    
    label console
        kernel linux
        append vga=normal initrd=initrd.gz  auto=true priority=critical
        
Если сервер, на который устанавливается ubuntu, находится за proxy, его поддержку следует добавить в syslinux.cfg: 
    
    :::text
    append vga=normal initrd=initrd.gz mirror/http/proxy="http://<proxy>:<port>" auto=true priority=critical
    
Сохранив новый iso и записав его на USB флешку, например, с помощью [UNetBootin](http://unetbootin.sourceforge.net/),  можно переходить к инсталляции операционной системы на компьютер.

При загрузке с инсталляционного образа система будет запрашивать IP адрес динамически у DHCP сервера, поэтому его наличие в сети крайне желательно. Так же с помощью него можно определить какой IP адрес был выделен. После получения IP адреса, в ходе инсталляции система подгрузит необходимое программное обеспечения для инсталляции системы через сетевую консоль. Спустя некоторое время, которое зависит от пропускной способности внешнего канала, сетевая консоль будет доступна:

    :::sh
    ssh installer@<ip адрес сервера>

Пароль: `r00tme`

Образы для i386 и для amd64,x86_64 платформ доступны в репозитории sources-ownport.

- [ubuntu-11.04-mini-netconsole-i386.iso](http://code.google.com/p/sources-ownport/downloads/detail?name=ubuntu-11.04-mini-netconsole-i386.iso&can=2&q=)
- [ubuntu-11.04-mini-netconsole-amd64.iso](http://code.google.com/p/sources-ownport/downloads/detail?name=ubuntu-11.04-mini-netconsole-amd64.iso&can=2&q=)

Не могу гарантировать, что данные образы будут работать на всех платформах. По крайней мере, на двух моих тестовых серверах на i386 и x86_64 установка прошла без проблем.

**Полезные ссылки:**

- [Debian-Installer: How to modify an existing CD image to preseed d-i](http://wiki.debian.org/DebianInstaller/Preseed/EditIso)
- [New Headless Ubuntu Server Guide](http://burk.crabula.com/index.php?title=New_Headless_Ubuntu_Server_Guide)
- [Linux Home Networking - Tutorials and Forums](http://www.linuxhomenetworking.com/)

