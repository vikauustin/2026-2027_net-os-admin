---
## Front matter
title: "Лабораторная работа №4"
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

Приобретение практических навыков по установке и базовому конфигурированию HTTP-сервера Apache.

# Задание

1. Установите необходимые для работы HTTP-сервера пакеты (см. раздел 4.4.1).
2. Запустите HTTP-сервер с базовой конфигурацией и проанализируйте его работу
(см. разделы 4.4.2 и 4.4.3).
3. Настройте виртуальный хостинг (см. раздел 4.4.4).
4. Напишите скрипт для Vagrant, фиксирующий действия по установке и настройке
HTTP-сервера во внутреннем окружении виртуальной машины server. Соответству-
ющим образом внесите изменения в Vagrantfile (см. раздел 4.4.5).

# Выполнение лабораторной работы

Установить необходимые для работы HTTP-сервера пакеты. перед установкой просмтореть список достпуных групп пакетов командой LANG=C yum grouplist(рис. [-@fig:001]).

![Выведен список групп пакетов среди которых server , minimal install, workstation,kde plasma workspaces](image/1.jpg){#fig:001 width=70%}

Установить стандартный веб-сервер командой dnf -y groupinstall "Basic Web Server(рис. [-@fig:002]).

![Установлены пакеты httpd, httpd-manual, mod_fcgid, mod_ssl и зависимости apr, apr-util, httpd-core, httpd-filesystem.](image/2.jpg){#fig:002 width=70%}

Просмотреть содержание конфигурационных файлов в каталогах /etc/httpd/conf и /etc/httpd/conf.d.(рис. [-@fig:003]).

![В /etc/httpd/conf находится httpd.conf и magic, в /etc/httpd/conf.d — autoindex.conf, fcgid.conf, manual.conf, ssl.conf, userdir.conf, welcome.conf.](image/3.jpg){#fig:003 width=70%}

Просмотреть и прокомментировать основный файл конфигурации веб-сервера httpd.conf.(рис. [-@fig:004]).

![В файле приведены директивы, управляющие работой сервера, со ссылками на документацию Apache 2.4.](image/4.jpg){#fig:004 width=70%}

Изучить директивы httpd.conf, в частности настройки прослушивания порта.(рис. [-@fig:005]).

![ Директива Listen 80 указывает, что сервер прослушивает 80-й порт по умолчанию.](image/5.jpg){#fig:005 width=70%}

Внести изменения в настройки межсетевого экрана узла server, разрешив работу с http.(рис. [-@fig:006]).

![Выполнены команды firewall-cmd --add-service=http и firewall-cmd --add-service=http --permanent.](image/6.jpg){#fig:006 width=70%}

Активировать и запустить HTTP-сервер командой systemctl start httpd и systemctl status httpd, убедиться, что он успешно запустился.(рис. [-@fig:007]).

![Служба httpd активна и работает, показаны дочерние процессы и PID.](image/7.jpg){#fig:007 width=70%}

На виртуальной машине server просмотреть лог ошибок работы веб-сервера командой tail -f /var/log/httpd/error_log.(рис. [-@fig:008]).

![В логе видно предупреждение о невозможности найти index.html в /var/www/html/](image/8.jpg){#fig:008 width=70%}

Запустить мониторинг доступа к веб-серверу командой tail -f /var/log/httpd/access_log и с клиента открыть страницу 192.168.1.1.(рис. [-@fig:009]).

![ В логе видны GET-запросы от клиента 192.168.1.30 к страницам /, /icons/poweredby.png и /favicon.ico.](image/9.jpg){#fig:009 width=70%}

На виртуальной машине client запустить браузер и в адресной строке ввести 192.168.1.1, проанализировать отобразившуюся страницу.(рис. [-@fig:010]).

![ В браузере открылась стандартная тестовая страница HTTP Server Test Page.](image/10.jpg){#fig:010 width=70%}

Остановить DNS-сервер и добавить запись для HTTP-сервера в конце файла прямой DNS-зоны /var/named/master/fz/vvustinova.net: www A 192.168.1.1.(рис. [-@fig:011]).

![ В файле прямой зоны добавлены записи для dhcp, ns, server и www с адресом 192.168.1.1.](image/11.jpg){#fig:011 width=70%}

Добавить запись для HTTP-сервера в конце файла обратной зоны /var/named/master/rz/192.168.1: 1 PTR www.vvustinova.net.(рис. [-@fig:012]).

![В файле обратной зоны добавлены PTR-записи для server, ns, dhcp, client и www.](image/12.jpg){#fig:012 width=70%}

Перезапустить DNS-сервер, удалить файлы журналов DNS и в каталоге /etc/httpd/conf.d создать файлы server.vvustinova.net.conf и www.vvustinova.net.conf.(рис. [-@fig:013]).

![Выполнены systemctl stop/start named, удаление .jnl-файлов, создание конфигурационных файлов.](image/13.jpg){#fig:013 width=70%}

Открыть на редактирование файл server.vvustinova.net.conf и внести настройки виртуального хоста.(рис. [-@fig:014]).

![В файле указаны VirtualHost *:80, ServerAdmin, DocumentRoot, ServerName, ErrorLog и CustomLog.](image/14.jpg){#fig:014 width=70%}

Открыть на редактирование файл www.vvustinova.net.conf и внести настройки виртуального хоста.(рис. [-@fig:015]).

![В файле указаны VirtualHost *:80, ServerAdmin, DocumentRoot, ServerName, ErrorLog и CustomLog.](image/15.jpg){#fig:015 width=70%}

Создать файл index.html в каталоге /var/www/html/server.vvustinova.net с приветственной строкой.(рис. [-@fig:016]).

![В файле записано Welcome to the server.vvustinova.net server.](image/16.jpg){#fig:016 width=70%}

 Создать файл index.html в каталоге /var/www/html/www.vvustinova.net с приветственной строкой.(рис. [-@fig:017]).

![В файле записано Welcome to the www.vvustinova.net server.](image/17.jpg){#fig:017 width=70%}

Проверить работу виртуального хостинга: в браузере открыть server.vvustinova.net.(рис. [-@fig:018]).

![ В браузере отобразилась страница Welcome to the server.vvustinova.net server.](image/18.jpg){#fig:018 width=70%}

Проверить работу виртуального хостинга: в браузере открыть www.vvustinova.net.(рис. [-@fig:019]).

![В браузере отобразилась страница Welcome to the www.vvustinova.net server.](image/19.jpg){#fig:019 width=70%}

Исправить права доступа и контекст SELinux для каталогов /var/www и /etc, затем перезапустить HTTP-сервер.
(рис. [-@fig:020]).

![Выполнены команды chown -R apache:apache /var/www, restorecon -vR /etc, /var/named, /var/www, systemctl restart httpd.](image/20.jpg){#fig:020 width=70%}

Создать исполняемый файл http.sh в каталоге /vagrant/provision/server, который повторяет действия по установке и настройке HTTP-сервера. В Vagrantfile добавить provision «server http».(рис. [-@fig:021]).

![В скрипте прописаны установка Basic Web Server, копирование конфигураций, права, restorecon, firewall и запуск службы. В Vagrantfile добавлен provision с путём provision/server/http.sh.](image/21.jpg){#fig:021 width=70%}

# Выводы

В ходе работы были приобретены практические навыки по установке и базовому конфигурированию HTTP-сервера Apache. Освоены запуск веб-сервера, анализ логов error_log и access_log, настройка межсетевого экрана и SELinux. Настроен виртуальный хостинг по двум DNS-адресам server.user.net и www.user.net, проверена его работа через браузер клиента. Создан скрипт автоматической установки и настройки HTTP-сервера для Vagrant.

#  Ответы на контрольные вопросы

1. Через какой порт по умолчанию работает Apache?

Порт 80 (HTTP). Для HTTPS используется порт 443.

2. Под каким пользователем запускается Apache и к какой группе относится этот пользователь?

Под пользователем apache, группа apache.

3. Где располагаются лог-файлы веб-сервера? Что можно по ним отслеживать?

В каталоге /var/log/httpd/: access_log (запросы клиентов), error_log (ошибки работы сервера).

4. Где по умолчанию содержится контент веб-серверов?

В каталоге /var/www/html/.

5. Каким образом реализуется виртуальный хостинг? Что он даёт?

С помощью блоков <VirtualHost> в конфигурационных файлах. Позволяет размещать несколько сайтов на одном IP-адресе, различая их по доменному имени.


