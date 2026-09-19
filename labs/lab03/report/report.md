---
## Front matter
title: "Лаборатораня работа №3"
subtitle: "Отчет"
author: "Устинова Виктория Вадимовна"

## Generic otions
lang: ru-RU
toc-title: "Содержание"

## Bibliography
bibliography: bib/cite.bib
csl: pandoc/csl/gost-r-7-0-5-2008-numeric.csl

## Pdf output format
toc: true # Table of contents
toc-depth: 2
lof: true # List of figures
lot: true # List of tables
fontsize: 12pt
linestretch: 1.5
papersize: a4
documentclass: scrreprt
## I18n polyglossia
polyglossia-lang:
  name: russian
  options:
	- spelling=modern
	- babelshorthands=true
polyglossia-otherlangs:
  name: english
## I18n babel
babel-lang: russian
babel-otherlangs: english
## Fonts
mainfont: IBM Plex Serif
romanfont: IBM Plex Serif
sansfont: IBM Plex Sans
monofont: IBM Plex Mono
mathfont: STIX Two Math
mainfontoptions: Ligatures=Common,Ligatures=TeX,Scale=0.94
romanfontoptions: Ligatures=Common,Ligatures=TeX,Scale=0.94
sansfontoptions: Ligatures=Common,Ligatures=TeX,Scale=MatchLowercase,Scale=0.94
monofontoptions: Scale=MatchLowercase,Scale=0.94,FakeStretch=0.9
mathfontoptions:
## Biblatex
biblatex: true
biblio-style: "gost-numeric"
biblatexoptions:
  - parentracker=true
  - backend=biber
  - hyperref=auto
  - language=auto
  - autolang=other*
  - citestyle=gost-numeric
## Pandoc-crossref LaTeX customization
figureTitle: "Рис."
tableTitle: "Таблица"
listingTitle: "Листинг"
lofTitle: "Список иллюстраций"
lotTitle: "Список таблиц"
lolTitle: "Листинги"
## Misc options
indent: true
header-includes:
  - \usepackage{indentfirst}
  - \usepackage{float} # keep figures where there are in the text
  - \floatplacement{figure}{H} # keep figures where there are in the text
---

# Цель работы

Приобретение практических навыков по установке и конфигурированию DHCP-сервера

# Задание

1. Установите на виртуальной машине server DHCP-сервер (см. раздел 3.4.1).
2. Настройте виртуальную машину server в качестве DHCP-сервера для виртуальной
внутренней сети (см. раздел 3.4.2).
3. Проверьте корректность работы DHCP-сервера в виртуальной внутренней сети
путём запуска виртуальной машины client и применения соответствующих утилит
диагностики (см. раздел 3.4.3).
4. Настройте обновление DNS-зоны при появлении в виртуальной внутренней сети
новых узлов (см. раздел 3.4.4).
5. Проверьте корректность работы DHCP-сервера и обновления DNS-зоны в виртуаль-
ной внутренней сети путём запуска виртуальной машины client и применения
соответствующих утилит диагностики (см. раздел 3.4.5).
6. Напишите скрипт для Vagrant, фиксирующий действия по установке и настройке
DHCP-сервера во внутреннем окружении виртуальной машины server. Соответ-
ствующим образом внести изменения в Vagrantfile (см. раздел 3.4.6).


# Выполнение лабораторной работы

Откройте файл /etc/kea/kea-dhcp4.conf на редактирование. В этом файле: замените шаблон для domain-name(рис. [-@fig:001]).

![В файле указываем опции domain-name-servers с адресами 192.168.1.1 и указываем vvustinova.net](image/1.jpg){#fig:001 width=70%}

На базе одного из примеров задать собственную конфигурацию dhcp-сети: адрес подсети 192.168.1.0/24, диапазон адресов для распределения клиентам 192.168.1.30–192.168.1.199..(рис. [-@fig:002]).

![В блоке subnet4 заданы id 1, subnet 192.168.1.0/24 и pools с диапазоном адресов.](image/2.jpg){#fig:002 width=70%}

Проверить правильность конфигурационного файла командой kea-dhcp4 -t /etc/kea/kea-dhcp4.conf, перезагрузить конфигурацию dhcpd и разрешить загрузку DHCP-сервера при запуске..(рис. [-@fig:003]).

![Проверка конфигурации прошла успешно, служба kea-dhcp4 включена в автозапуск.](image/3.jpg){#fig:003 width=70%}

Добавить запись для DHCP-сервера в конце файла прямой DNS-зоны /var/named/master/fz/vvustinova.net: dhcp A 192.1681.(рис. [-@fig:004]).

![В файле прямой зоны добавлена A-запись dhcp с адресом 192.168.1.1.](image/4.jpg){#fig:004 width=70%}

Добавить запись для DHCP-сервера в конце файла обратной зоны /var/named/master/rz/192.168. 1 PTR dhcp.vvustinovanet.(рис. [-@fig:005]).

![В файле обратной зоны добавлены PTR-записи для server, ns и dhcp](image/5.jpg){#fig:005 width=70%}

Перезапустить named и проверить, что можно обратиться к DHCP-серверу по имени командой ping dhcp.vvustinovanet.(рис. [-@fig:006]).

![Пинг до dhcp.vvustinova.net успешно проходит, адрес 192.168.1.1.](image/6.jpg){#fig:006 width=70%}

Внести изменения в настройки межсетевого экрана узла server, разрешив работу с DHCP.(рис. [-@fig:007]).

![Выполнены команды firewall-cmd --add-service=dhcp и с флагом --permanent.](image/7.jpg){#fig:007 width=70%}

В дополнительном терминале запустить мониторинг происходящих в системе процессов в реальном времени командой tail -f /var/log/messages.(рис. [-@fig:008]).

![В логе видны записи о запуске systemd-hostnamed и serial-getty.](image/8.jpg){#fig:008 width=70%}

Создать файл 01-routing.sh в каталоге provision/client, который изменяет настройки NetworkManager так, чтобы весь трафик на клиенте шёл по умолчанию через интерфейс eth1.(рис. [-@fig:009]).

![В скрипте прописаны команды nmcli для настройки шлюза и отключения default-маршрута на eth0.](image/9.jpg){#fig:009 width=70%}

В Vagrantfile подключить скрипт 01-routing.sh в разделе конфигурации для клиента.(рис. [-@fig:010]).

![ В Vagrantfile добавлен provision «client routing» с путём к скрипту.](image/10.jpg){#fig:010 width=70%}

Зафиксировать внесённые изменения для внутренних настроек виртуальной машины client и запустить её командой vagrant up client -provision.(рис. [-@fig:011]).

![Выполнен restorecon, затем запуск клиента с провижинингом.](image/11.jpg){#fig:011 width=70%}

На машине server посмотреть список выданных адресов командой cat /var/lib/kea/kea-leases4.csv и прокомментировать информацию.(рис. [-@fig:012]).

![В файле аренды видна выдача адреса 192.168.1.30 клиенту с MAC-адресом 08:00:27:e8:eb:81.](image/12.jpg){#fig:012 width=70%}

Bойти в систему виртуальной машины client под своим пользователем и ввести ifconfig для просмотра информации об интерфейсах.(рис. [-@fig:013]).

![Интерфейс eth1 получил адрес 192.168.1.30/24, выданный DHCP-сервером.](image/13.jpg){#fig:013 width=70%}

Создать ключ на сервере с Bind9 для обновления DNS-зоны: tsig-keygen (рис. [-@fig:014]).

![Создан каталог keys и сгенерирован ключ dhcp_updater.key.](image/14.jpg){#fig:014 width=70%}

В файле /etc/named/vvustinova.net разрешить обновление зоны, прописав update-policy с grant DHCP_UPDATE для прямых и обратных зон.(рис. [-@fig:015]).

![В зонах vvustinova.net и 1.168.192.in-addr.arpa добавлены политики обновления.](image/15.jpg){#fig:015 width=70%}

Подключить ключ в файле /etc/named.conf через директиву include и указать listen-on с any.(рис. [-@fig:016]).

![В named.conf добавлен include ключа, listen-on port 53 с 127.0.0.1 и any.](image/16.jpg){#fig:016 width=70%}

Сформировать ключ для Kea в файле /etc/kea/tsig-keys.json, указав имя DHCP_UPDATE, алгоритм hmac-sha512 и секрет.(рис. [-@fig:017]).

![В файле tsig-keys.json прописан ключ DHCP_UPDATE.](image/17.jpg){#fig:017 width=70%}

Настроить файл /etc/kea/kea-dhcp-ddns.conf: указать ip-address 1270.0.1, port 53001, control-socket, forward-ddns и reverse-ddns.(рис. [-@fig:018]).

![В конфигурации DDNS заданы forward-ddns для vvustinova.net и reverse-ddns для 1.168.192.in-addr.arpa.](image/18.jpg){#fig:018 width=70%}

Проверить файл на наличие возможных синтаксических ошибок командой kea-dhcp-ddns -t /etc/kea/kea-dhcp-ddns.conf.(рис. [-@fig:019]).

![ Проверка конфигурации kea-dhcp-ddns прошла успешно.](image/19.jpg){#fig:019 width=70%}

Запустить службу ddns командой systemctl enable -now kea-dhcp-ddns.service и проверить статус работы службы.(рис. [-@fig:020]).

![Служба kea-dhcp-ddns активна и работает.](image/20.jpg){#fig:020 width=70%}

Bнести изменения в конфигурационный файл /etc/kea/kea-dhcp4.conf, добавив в него разрешение на динамическое обновление DNS-записей с локального узла прямой и обратной зон.(рис. [-@fig:021]).

![В блоке Dhcp4 добавлен dhcp-ddns с enable-updates true и ddns-qualifying-suffix vvustinova.net.](image/21.jpg){#fig:021 width=70%}

Проверить файл на наличие возможных синтаксических ошибок командой kea-dhcp4 -t /etc/kea/kea-dhcp4.conf.(рис. [-@fig:022]).

![Проверка конфигурации kea-dhcp4 прошла успешно.](image/22.jpg){#fig:022 width=70%}

Перезапустить DHCP-сервер и проверить статус работы командой systemctl status kea-dhcp4.service.(рис. [-@fig:023]).

![Служба kea-dhcp4 перезапущена и активна.](image/23.jpg){#fig:023 width=70%}

Убедиться, что в файле kea-dhcp-ddns.conf корректно прописан блок tsig-keys с ключом DHCP_UPDATE и forward-ddns для домена vvustinova.net.(рис. [-@fig:024]).

![В файле видны ip-address, port, control-socket и tsig-keys.](image/24.jpg){#fig:024 width=70%}

На машине client переполучить адрес командами nmcli connection down eth1 и nmcli connection up eth1, затем с помощью утилиты dig убедиться в наличии DNS-записи о клиенте в прямой DNS-зоне.(рис. [-@fig:025]).

![Выполнен запрос dig @192.168.1.1 client.vvustinova.net, получен ответ.](image/25.jpg){#fig:025 width=70%}

На виртуальной машине server перейти в каталог /vagrant/provision/server/, создать в нём каталог dhcp и поместить туда конфигурационные файлы DHCP. Заменить конфигурационные файлы DNS-сервера.(рис. [-@fig:026]).

![Выполнено копирование файлов kea и named в каталог provision.](image/26.jpg){#fig:026 width=70%}

 В каталоге /vagrant/provision/server создать исполняемый файл dhcp.sh, который повторяет действия по установке и настройке DHCP-сервера.(рис. [-@fig:027]).

![В скрипте прописаны установка kea, копирование конфигураций, права, firewall и запуск служб.](image/27.jpg){#fig:027 width=70%}

Для отработки созданного скрипта во время загрузки виртуальной машины server добавить в Vagrantfile в разделе конфигурации для сервера provision «server dhcp».(рис. [-@fig:028]).

![В Vagrantfile добавлены provision «server dns» и «server dhcp» с путями к скриптам.](image/28.jpg){#fig:028 width=70%}

Применить изменения командой vagrant reload server -provision для перезагрузки сервера с применением скриптов настройки.
(рис. [-@fig:029]).

![Выполнен vagrant reload server --provision, настройки применяются.](image/29.jpg){#fig:029 width=70%}

# Выводы

В ходе работы были приобретены
практические навыки по установке и конфигурированию DHCP-сервера Kea. Освоены настройка подсети, диапазона адресов, шлюза и DNS-сервера, а также выдача адресов клиентам. Настроено динамическое обновление DNS-зоны при появлении новых узлов с использованием ключей TSIG. Проверена корректность работы DHCP-сервера и обновления DNS-записей. Создан скрипт автоматической настройки DHCP-сервера для Vagrant.

# Ответы

1. В каких файлах хранятся настройки сетевых подключений?

В файлах /etc/sysconfig/network-scripts/ifcfg-*, /etc/NetworkManager/system-connections/, а также в /etc/resolv.conf и /etc/named.conf.

2. За что отвечает протокол DHCP?

DHCP (Dynamic Host Configuration Protocol) — сетевой протокол, позволяющий компьютерам автоматически получать IP-адрес и другие параметры, необходимые для работы в сети TCP/IP.

3. Поясните принципы работы протокола DHCP. Какими сообщениями обмениваются клиент и сервер?

Клиент отправляет широковещательный запрос DHCPDISCOVER, сервер отвечает DHCPOFFER, клиент запрашивает DHCPREQUEST, сервер подтверждает DHCPACK. При необходимости — DHCPNAK, DHCPDECLINE, DHCPRELEASE.

4. В каких файлах обычно находятся настройки DHCP-сервера? За что отвечает каждый из файлов?

/etc/kea/kea-dhcp4.conf — основные настройки DHCP; /etc/kea/kea-dhcp-ddns.conf — настройки динамического обновления DNS; /etc/kea/tsig-keys.json — ключи TSIG; /var/lib/kea/kea-leases4.csv — база выданных адресов.

5. Что такое DDNS? Для чего применяется DDNS?

DDNS (Dynamic DNS) — технология, позволяющая автоматически обновлять DNS-записи при изменении IP-адреса узла. Применяется для доступа к узлам с динамическими адресами по постоянному доменному имени.

6. Какую информацию можно получить, используя утилиту ifconfig?

Информацию о сетевых интерфейсах: IP-адрес, маска, MAC-адрес, состояние, статистика. Примеры: ifconfig, ifconfig eth0, ifconfig -a.

7. Какую информацию можно получить, используя утилиту ping?

Проверку соединения с узлом, время задержки (RTT), потери пакетов. Примеры: ping 192.168.1, ping -c 4 server.vvustinovanet.


