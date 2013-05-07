Title: Установка Ubuntu 12.04: жесткий диск + сетевая консоль
Author: d.rey
Date: 2012-05-03 06:57:00
Slug: ubuntu-1204
Tags: install,network,linux,ubuntu,console

Прошел почти год с публикации поста про [установку Ubuntu с сетевой консоли](http://devel.ownport.net/2011/05/ubuntu-1104.html). Несколько раз [инсталляционные образы](http://code.google.com/p/sources-ownport/downloads/list) выручали, когда нужно было установить систему без монитора и клавиатуры. Но несколько дней назад понадобилось установить Ubuntu на компьютер, который планировалось использовать как небольшой сервер. Монитора и клавиатуры под рукой не оказалось, да и загрузка с USB Drive в BIOS выключена. Безвыходных ситуаций не бывает, поэтому и решение нашлось - установка Ubuntu 12.04 с жесткого диска.

Основная задача: обеспечить возможность загрузки с жесткого диска, дальнейшая установка должна быть выполнена через сетевую консоль.

Для этого необходимо:

- [минимальный образ Ubuntu 12.04](https://help.ubuntu.com/community/Installation/MinimalCD) 
- внешний карман для жесткого 3,5” диска. В моем случае использовался [SunBright](http://www.welland.com.tw/html/enclosure/700.html), поддерживающий как IDE так и SATA интерфейсы
- компьютер или лептоп с установленым Linux. В примерах ниже используется Ubuntu 12.04 Desktop
- [VirtualBox](https://www.virtualbox.org/), опционально, необходим для проверки работы и тестирования.

Из компьютера, на который планируется установка Ubuntu, необходимо извлечь жесткий диск и подключить через внешний карман c USB интерфейсом к компьютеру или лептопу с предустановленной Ubuntu. Если на жестком диске есть важные данные, лучше сделать их резервную копию. С помощью fdisk иди gparted http://gparted.sourceforge.net/ делается разбивка файловой системы. Для упрощения можно выделить два раздела: один для swap, другой для root (/). Для root выбираем ext4 файловую систему. Важно не забыть пометить root раздел как загрузочный (Boot).

Далее необходимо установить grub на новый раздел (/media/newroot -> /dev/sdb2): 

    :::text
    # sudo grub-install --root-directory /media/newroot /dev/sdb

где /dev/sdb - диск на который будет установлен Grub. Необходимо быть очень аккуратным при выполнении команд, что бы не перезаписать существующие разделы на рабочем компьютере

Как результат, после выполнения команды на диске будет создана директория /boot/grub с множеством файлов, необходимых для работы grub. Для нас будет важен только файл /boot/grub/grub.cfg. Перед тем, как вносить изменения в конфигурационый файл, необходимо скопировать два файла linux и initrd.gz из образа для Ubuntu 12.04 (ubuntu-12.04-mini-i386.iso) в директорию /boot/newinstall. После этого пераписываем файл grub.cfg на /dev/sdb2, файлом: 

    :::text
    set root=(hd0,2) # значение (hd0,2) необходимо переназначить в 
                     # зависимости от актуальной конфигурации 
    linux /boot/newinstall/linux \
          locale=en_US  \
          console-setup/ask_detect=false \
          console-setup/layoutcode=ru \
          anna/choose_modules=network-console \
          network-console/password=r00tme \
          network-console/password-again=r00tme \
          debconf/priority=critical --
    initrd /boot/newinstall/initrd.gz
    boot 
    
По сути, это все. Можно подключать диск компьютеру и выполнять загрузку. Если все хорошо, то буквально через пару минут вы сможете подключиться к новому серверу через ssh (login: installer, password: r00tme): 

    :::text
    # ssh installer@<server_ip_address> 
    
и продолжить инсталяцию. 

**Опционально**. Перед установка жесткого диска в сервер, новую конфигурацию можно проверить с помощью VirtualBox. Дело в том, что VirtualBox позволяет работать с реальными разделами на жестких дисках через vmdk файлы. Для создания такого файла необходимо выполнить следующую команду: 

    :::text
    # sudo vboxmanage internalcommands createrawvmdk -filename usb-hdd320.vmdk -rawdisk /dev/sdb 
    
    RAW host disk access VMDK file usb-hdd320.vmdk created successfully. 
    
Далее необходимо создать виртуальную машину для работы с жестким диском через vmdk файл 
    
    :::text
    sudo vboxmanage createvm --name minilab --ostype Ubuntu --register
    sudo vboxmanage modifyvm minilab --memory 512
    sudo vboxmanage storagectl minilab --name "IDE Controller" --add ide
    sudo vboxmanage storageattach minilab --storagectl "IDE Controller" --port=0 --device=0 --type hdd --medium /media/data/vm/usb-hdd320.vmdk 
    
Запускается виртуальная машина minilab с помощью: 
    
    :::text
    sudo vboxmanage startvm minilab
    
Если все хорошо, то через пару минут в окне виртульный машины вы увидите приглашение продолжить установку через сетевую консоль: ssh installer@<vm_ip_address>

**Ссылки**

- [VirtualBox headless setup](http://www.bloof.de/virtualBox_headless)
- [Автоматическая преднастроенная установка Ubuntu: isolinux и preseed](http://habrahabr.ru/post/104029/)
- [Preseed example, Ubuntu 12.04, installation guideline](https://help.ubuntu.com/12.04/installation-guide/example-preseed.txt)
- [Ubuntu 12.04 releases](http://releases.ubuntu.com/12.04/)

