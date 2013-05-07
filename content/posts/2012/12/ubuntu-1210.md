Title: Сетевая установка Ubuntu 12.10 
Author: d.rey
Date: 2012-12-31 07:48:00
Slug: ubuntu-1210
Tags: install,network,ubuntu

Недавно нужно было установить [Ubuntu](http://www.ubuntu.com/) на лептоп. Казалось на первый взгляд простая задача обернулась небольшими сложностями. Вопрос в том, что согласно политик безопасности загрузка операционной системы для этого лептопа возможна только с жесткого диска или по сети, а изменение параметров BIOS закрыт по паролю.

Поиск в Google дал следующие ссылки по инсталяции Ubuntu по сети:

- [Ubuntu installation](https://help.ubuntu.com/community/Installation)
- [Installation/LocalNet](https://help.ubuntu.com/community/Installation/LocalNet)
- [Installation/Netboot](https://help.ubuntu.com/community/Installation/Netboot)
- [Installation/QuickNetboot](https://help.ubuntu.com/community/Installation/QuickNetboot)
- [Установка Ubuntu по сети](http://help.ubuntu.ru/wiki/%D1%83%D1%81%D1%82%D0%B0%D0%BD%D0%BE%D0%B2%D0%BA%D0%B0_ubuntu_%D0%BF%D0%BE_%D1%81%D0%B5%D1%82%D0%B8)
- [Ubuntu releases](http://releases.ubuntu.com/)
- [Images/x86](http://archive.ubuntu.com/ubuntu/dists/quantal/main/installer-i386/)
- [Images/x86_64](http://archive.ubuntu.com/ubuntu/dists/quantal/main/installer-amd64/)

Самый простой путь установки системы описан в [Installation/QuickNetboot](https://help.ubuntu.com/community/Installation/QuickNetboot). Однако и его можно упростить. Из описанных в руководстве инструментов нужен только [dnsmasq](http://www.thekelleys.org.uk/dnsmasq/doc.html). Он и DNS и TFTP и DHCP сервер. Установка dnsmasq:

    :::text
    apt-get install dnsmasq
    
Для загрузки системы по сети необходим DHCP сервер для выделения IP адреса и TFTP сервер, на котором хранятся файлы загрузки. С DHCP сервером могут возникнуть сложности, если динамическое выделение адресов уже используется в локальной мети, но управлять существующим DHCP сервером вы не можете. С таким вопросом я как раз и столкнулся. Поэтому, я использовал свой домащний роутер. WAN интерфейсом он смотрел в сторону локальной сети, LAN интерфейсы использовались для подключения лептопа и TFTP сервера. DHCP сервер на базе dnsmasq уже был запущен на домашнем роутере. Поэтому потребовалось только указать параметы зарузки в оболочке [DD-WRT](http://www.dd-wrt.com/site/index): Services -> DNSMasq -> Additional DNSMasq Options:

    :::text
    dhcp-boot=pxelinux.0,tftpsrv,192.168.1.2
    
где

- pxelinux.0 - загрузочный образ,
- tftpsrv и 192.168.1.2 - имя сервера и IP адрес хоста на котором запущен TFTP сервер.

В качестве TFTP сервера была попытка использовать atftp из руководства [Installation/QuickNetboot](https://help.ubuntu.com/community/Installation/QuickNetboot), затем tftpd-hpa из "[Установка Ubuntu по сети](http://help.ubuntu.ru/wiki/%D1%83%D1%81%D1%82%D0%B0%D0%BD%D0%BE%D0%B2%D0%BA%D0%B0_ubuntu_%D0%BF%D0%BE_%D1%81%D0%B5%D1%82%D0%B8)". К сожалению обе они были неудачными, операционная система упорно не хотела запускаться с них. Время поджимало, поэтому был использован проверенный вариант с TFTP сервером, встроенным в DNSMasq. Необходимо было только изменить 2 строки в файле конфигураций /etc/dnsmasq.conf 

    :::text
    enable-tftp
    tftp-root=/tftpboot
    
и перезапустить сервис 

    :::text
    $ sudo service dnsmasq restart
    
/tftpboot - это директория, где расположены файлы, для загрузки по сети
 
    :::text
    mkdir /tftpboot
    cd /tftpboot
    wget http://archive.ubuntu.com/ubuntu/dists/quantal/main/installer-i386/current/images/netboot/netboot.tar.gz
    tar -zxf netboot.tar.gz
    chown -R nobody: .
    
Теперь достаточно включить инсталируемый лептоп и выбрать загрузку по сети.

**Дополнение**: Для сокращения времени на загрузку данных из Интернет, кроме загрузочного образа можно подмонтировать ISO образ с Ubuntu 12.10 к tftpboot директории 

    :::
    mkdir /tftpboot/ubuntu 
    mount -o loop /media/distfiles/ubuntu-12.10-alternate-i386.iso /tftpboot/ubuntu/
    
