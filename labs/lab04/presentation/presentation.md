---
## Front matter
lang: ru-RU
title: Лабораторная работа №4
subtitle: Базовая настройка HTTP-сервера Apache
author:
  - Устинова В. В.
institute:
  - Российский университет дружбы народов, Москва, Россия
date: 26 сентября 2026

## i18n babel
babel-lang: russian
babel-otherlangs: english

## Formatting pdf
toc: false
toc-title: Содержание
slide_level: 2
aspectratio: 169
section-titles: true
theme: metropolis
header-includes:
 - \metroset{progressbar=frametitle,sectionpage=progressbar,numbering=fraction}
---

# Информация

## Докладчик

:::::::::::::: {.columns align=center}
::: {.column width="70%"}

  * Устинова Виктория Вадимовна
  * студент НПИбд-01-24
  * Российский университет дружбы народов

:::
::: {.column width="30%"}

:::
::::::::::::::

## Цель работы

Приобретение практических навыков по установке и базовому конфигурированию HTTP-сервера Apache.

## Задание

1. Установите необходимые для работы HTTP-сервера пакеты (см. раздел 4.4.1).
2. Запустите HTTP-сервер с базовой конфигурацией и проанализируйте его работу
(см. разделы 4.4.2 и 4.4.3).
3. Настройте виртуальный хостинг (см. раздел 4.4.4).
4. Напишите скрипт для Vagrant, фиксирующий действия по установке и настройке
HTTP-сервера во внутреннем окружении виртуальной машины server. Соответству-
ющим образом внесите изменения в Vagrantfile (см. раздел 4.4.5).

## Выполнение лабораторной работы

Установить необходимые для работы HTTP-сервера пакеты. перед установкой просмтореть список достпуных групп пакетов командой LANG=C yum grouplist

![Выведен список групп пакетов среди которых server , minimal install, workstation,kde plasma workspaces](image/1.jpg){#fig:001 width=70%}

## Выполнение лабораторной работы

Установить стандартный веб-сервер командой dnf -y groupinstall "Basic Web Server

![Установлены пакеты httpd, httpd-manual, mod_fcgid, mod_ssl и зависимости apr, apr-util, httpd-core, httpd-filesystem.](image/2.jpg){#fig:002 width=70%}

## Выполнение лабораторной работы

Просмотреть содержание конфигурационных файлов в каталогах /etc/httpd/conf и /etc/httpd/conf.d

![В /etc/httpd/conf находится httpd.conf и magic, в /etc/httpd/conf.d — autoindex.conf, fcgid.conf, manual.conf, ssl.conf, userdir.conf, welcome.conf.](image/3.jpg){#fig:003 width=70%}

## Выполнение лабораторной работы

Просмотреть и прокомментировать основный файл конфигурации веб-сервера httpd.conf

![В файле приведены директивы, управляющие работой сервера, со ссылками на документацию Apache 2.4.](image/4.jpg){#fig:004 width=70%}

## Выполнение лабораторной работы

Изучить директивы httpd.conf, в частности настройки прослушивания портa

![ Директива Listen 80 указывает, что сервер прослушивает 80-й порт по умолчанию.](image/5.jpg){#fig:005 width=70%}

## Выполнение лабораторной работы

Внести изменения в настройки межсетевого экрана узла server, разрешив работу с http.

![Выполнены команды firewall-cmd --add-service=http и firewall-cmd --add-service=http --permanent.](image/6.jpg){#fig:006 width=70%}

## Выполнение лабораторной работы

Активировать и запустить HTTP-сервер командой systemctl start httpd и systemctl status httpd, убедиться, что он успешно запустился

![Служба httpd активна и работает, показаны дочерние процессы и PID.](image/7.jpg){#fig:007 width=70%}

## Выполнение лабораторной работы

На виртуальной машине server просмотреть лог ошибок работы веб-сервера командой tail -f /var/log/httpd/error_log.

![В логе видно предупреждение о невозможности найти index.html в /var/www/html/](image/8.jpg){#fig:008 width=70%}

## Выполнение лабораторной работы

Запустить мониторинг доступа к веб-серверу командой tail -f /var/log/httpd/access_log и с клиента открыть страницу 192.168.1.1

![ В логе видны GET-запросы от клиента 192.168.1.30 к страницам /, /icons/poweredby.png и /favicon.ico.](image/9.jpg){#fig:009 width=70%}

## Выполнение лабораторной работы

На виртуальной машине client запустить браузер и в адресной строке ввести 192.168.1.1, проанализировать отобразившуюся страницу

![ В браузере открылась стандартная тестовая страница HTTP Server Test Page.](image/10.jpg){#fig:010 width=70%}

## Выполнение лабораторной работы

Остановить DNS-сервер и добавить запись для HTTP-сервера в конце файла прямой DNS-зоны /var/named/master/fz/vvustinova.net: www A 192.168.1.1

![ В файле прямой зоны добавлены записи для dhcp, ns, server и www с адресом 192.168.1.1.](image/11.jpg){#fig:011 width=70%}

## Выполнение лабораторной работы

Добавить запись для HTTP-сервера в конце файла обратной зоны /var/named/master/rz/192.168.1: 1 PTR www.vvustinova.net.

![В файле обратной зоны добавлены PTR-записи для server, ns, dhcp, client и www.](image/12.jpg){#fig:012 width=70%}

## Выполнение лабораторной работы

Перезапустить DNS-сервер, удалить файлы журналов DNS и в каталоге /etc/httpd/conf.d создать файлы server.vvustinova.net.conf и www.vvustinova.net.conf

![Выполнены systemctl stop/start named, удаление .jnl-файлов, создание конфигурационных файлов.](image/13.jpg){#fig:013 width=70%}

## Выполнение лабораторной работы

Открыть на редактирование файл server.vvustinova.net.conf и внести настройки виртуального хостa

![В файле указаны VirtualHost *:80, ServerAdmin, DocumentRoot, ServerName, ErrorLog и CustomLog.](image/14.jpg){#fig:014 width=70%}

## Выполнение лабораторной работы

Открыть на редактирование файл www.vvustinova.net.conf и внести настройки виртуального хоста.

![В файле указаны VirtualHost *:80, ServerAdmin, DocumentRoot, ServerName, ErrorLog и CustomLog.](image/15.jpg){#fig:015 width=70%}

## Выполнение лабораторной работы

Создать файл index.html в каталоге /var/www/html/server.vvustinova.net с приветственной строкой.

![В файле записано Welcome to the server.vvustinova.net server.](image/16.jpg){#fig:016 width=70%}

## Выполнение лабораторной работы

 Создать файл index.html в каталоге /var/www/html/www.vvustinova.net с приветственной строкой

![В файле записано Welcome to the www.vvustinova.net server.](image/17.jpg){#fig:017 width=70%}

## Выполнение лабораторной работы

Проверить работу виртуального хостинга: в браузере открыть server.vvustinova.net

![ В браузере отобразилась страница Welcome to the server.vvustinova.net server.](image/18.jpg){#fig:018 width=70%}

## Выполнение лабораторной работы

Проверить работу виртуального хостинга: в браузере открыть www.vvustinova.net

![В браузере отобразилась страница Welcome to the www.vvustinova.net server.](image/19.jpg){#fig:019 width=70%}

## Выполнение лабораторной работы

Исправить права доступа и контекст SELinux для каталогов /var/www и /etc, затем перезапустить HTTP-сервер.

![Выполнены команды chown -R apache:apache /var/www, restorecon -vR /etc, /var/named, /var/www, systemctl restart httpd.](image/20.jpg){#fig:020 width=70%}

## Выполнение лабораторной работы

Создать исполняемый файл http.sh в каталоге /vagrant/provision/server, который повторяет действия по установке и настройке HTTP-сервера. В Vagrantfile добавить provision «server http».

![В скрипте прописаны установка Basic Web Server, копирование конфигураций, права, restorecon, firewall и запуск службы. В Vagrantfile добавлен provision с путём provision/server/http.sh.](image/21.jpg){#fig:021 width=70%}

## Выводы

В ходе работы были приобретены практические навыки по установке и базовому конфигурированию HTTP-сервера Apache. Освоены запуск веб-сервера, анализ логов error_log и access_log, настройка межсетевого экрана и SELinux. Настроен виртуальный хостинг по двум DNS-адресам server.user.net и www.user.net, проверена его работа через браузер клиента. Создан скрипт автоматической установки и настройки HTTP-сервера для Vagrant.
