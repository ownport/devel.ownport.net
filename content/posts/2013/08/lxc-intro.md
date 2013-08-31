Title: Linux Container (LXC), контейнерная виртуализация
Author: d.rey
Date: 2013-08-21 22:23:00
Slug: lxc-intro
Tags: linux, container, lxc, cloud, virtualization, ubuntu

Подборка кратких аннотаций, заметок, ссылок на документацию, статьи, видео, посвященные Linux Containers.

[LXC/Linux Containers](http://lxc.sourceforge.net/) представляют собой технологию виртуализации на уровне операционной системы для запуска нескольких изолированных экземпляров Linux на одном компьютере, на основе [namespaces](http://en.wikipedia.org/wiki/Cgroups#Namespace_isolation) и [cgroups](https://www.kernel.org/doc/Documentation/cgroups/cgroups.txt). LXC не использует виртуальные машины, а создаёт виртуальное окружение с собственным пространством процессов и сетевым стеком. Все экземпляры LXC используют один экземпляр ядра ОС. Благодаря этому, вместо организации полной изоляции, LXC использует эффективные механизмы, позволяющие поддерживать работу тысячи эмулилированных систем на одном сервере.

Группы управления (cgroups) - это способность ядра предоставлять возможность иерархически группировать задачи, а также назначать и ограничивать ресурсы для каждой группы управления. Они используются в контейнерах для ограничения доступа к блочным и посимвольным устройствам (block and character device) и для заморозки (приостановки) контейнеров. В дальнейшем их можно использовать для ограничения используемой памяти и блочного ввода/вывода, гарантированного минимума использования CPU и для фиксирования определенного количество CPU за отдельными контейнерами. Все квоты и ограничения контейнера могут быть изменены без необходимости рестарта.

Сетевое пространство имен контейнера содержит свой набор сетевых ресурсов: устройтсва, IP адреса, маршруты, сокеты и т.д. Другие процессы за пределами адресного пространства не могут доступаться к этим ресурсам, так как они ничего не знают о них.

Для упрощения работы с контейнерами можно воспользоваться: 

- скриптом [lxc-ubuntu-x](http://derek.simkowiak.net/lxc-ubuntu-x/) для гибкого создания контейнеров на основе существующих контейнеров и шаблонов 
- [LXC Web Panel](http://lxc-webpanel.github.io/) -  веб-панель для управления и мониторинга контейнерами через веб-браузер.
- [lxctl](https://github.com/lxctl/lxctl)

Хороший обучающий материал на английском по Linux контейнерам, [LXC tutorial](http://www.janoszen.com/2013/05/14/lxc-tutorial/). Содержит как теоритическую часть, с пояснениями работы LXC, так и практические примеры создания, конфигурирования, управления контейнерами. На русском языке, есть [статья](http://www.3dnews.ru/619981) описывающая весь процесс создания тестовой платформы на базе VirtualBox для изучения работы с контейнерами.

Виртуализация в современных информационных системах используется повсеместно. Применяя ее, можно упростить решение целого класса задач, но для этого необходимо понимать достоинства, недостатки и ограничения разных технологий виртуализации. В докладе Рашита Азизбаева (Яндекс), [Виртуализация в Linux ](http://events.yandex.ru/events/kit/3/talks/525/) рассказывается о существующих типах виртуализации, их особенностях, а также инструментах управления виртуальными машинами.

## Альтернативы LXC: 

- [OpenVZ](http://wiki.openvz.org/)
- [Linux-VServer](http://linux-vserver.org/)
- [FreeBSD Jails](http://www.freebsd.org/doc/en/books/handbook/jails.html)

## Видео на youtube:

- [Linux Container (lxc) demonstration](http://www.youtube.com/watch?v=N7GkNs2wQt4)

## Ссылки

- [LXC Overview](https://help.ubuntu.com/community/LXC)
- [LXC HOWTO](http://lxc.teegra.net/)
- [Ubuntu 12.04/Ubuntu Server Guide/Virtualization/LXC](https://help.ubuntu.com/lts/serverguide/lxc.html)
- [Sysadmin Cookbook by Dobrica Pavlinusic](http://sysadmin-cookbook.rot13.org/#lxc)
- [ArchLinux/Linux Containers](https://wiki.archlinux.org/index.php/Linux_Containers)
- [LXC vs. OpenVZ](http://www.janoszen.com/2013/01/22/lxc-vs-openvz/)
- [What is LXC and how to get started?](http://askubuntu.com/questions/293275/what-is-lxc-and-how-to-get-started)
- [LXC containers or extremely fast virtualization](http://www.stgraber.org/2009/11/06/lxc-containers-or-extremely-fast-virtualization/)
- [LXC – Linux Containers on Steroids](http://www.janoszen.com/2012/06/04/lxc-linux-containers-on-steroids/)
- [Booting an Ubuntu 12.04 virtual machine in an LXC container](https://www.stgraber.org/2012/03/04/booting-an-ubuntu-12-04-virtual-machine-in-an-lxc-container/)
- [LXC in Ubuntu 12.04 LTS](https://www.stgraber.org/2012/05/04/lxc-in-ubuntu-12-04-lts/)
- [Руководство по защищенным Linux-контейнерам](http://www.ibm.com/developerworks/ru/library/l-lxc-security/) & [Secure Linux containers cookbook](http://www.ibm.com/developerworks/linux/library/l-lxc-security/index.html)
- [LXC: Linux container tools](https://www.ibm.com/developerworks/linux/library/l-lxc-containers/)
- [Devops: From OpenVZ to LXC on Debian & Ubuntu](https://lists.metalab.at/pipermail/devops/2012-July/000127.html)
- [Cgroups explained: Limiting Linux Processes](http://www.janoszen.com/2013/02/06/limiting-linux-processes-cgroups-explained/)
- [libvirt](http://libvirt.org/index.html)

