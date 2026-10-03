---
## Front matter
lang: ru-RU
title: Лабораторная работа №6
subtitle: Установка и настройка системы управления базами данных MariaDB
author:
 - Устинова В. В.
institute:
  - Российский университет дружбы народов, Москва, Россия
date: 03 октября 2026

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

Приобретение практических навыков по установке и конфигурированию системы управления базами данных на примере программного обеспечения MariaDB.

## Задание

1. Установите необходимые для работы MariaDB пакеты (см. раздел 6.4.1).
2. Настройте в качестве кодировки символов по умолчанию utf8 в базах данных.
3. В базе данных MariaDB создайте тестовую базу addressbook, содержащую таблицу
city с полями name и city, т.е., например, для некоторого сотрудника указан город,
в котором он работает (см. раздел 6.4.1).
4. Создайте резервную копию базы данных addressbook и восстановите из неё данные
(см. раздел 6.4.1).
5. Напишите скрипт для Vagrant, фиксирующий действия по установке и настройке
базы данных MariaDB во внутреннем окружении виртуальной машины server. Соответствующим образом следует внести изменения в Vagrantfile (см. раздел 6.4.5).

## Выполнение лабораторной работыПросмотр конфигурационных файлов MariaDB.

Просмотрите конфигурационные файлы mariadb в каталоге /etc/my.cnf.d и в файле
/etc/my.cnf. В отчёте прокомментируйте построчно их содержание

![В /etc/my.cnf указаны группы [client-server] и директива !includedir /etc/my.cnf.d. В каталоге /etc/my.cnf.d находятся файлы: auth_gssapi.cnf, client.cnf, enable_encryption.preset, mariadb-server.cnf, mysql-clients.cnf, provider_bzip2.cnf, provider_lz4.cnf, provider_lzo.cnf, provider_snappy.cnf, spider.cnf.](image/1.jpg){#fig:001 width=70%}

## Содержимое каталога my.cnf.d.

Просмотреть содержание конфигурационных файлов MariaDB в каталоге /etc/my.cnf.d и прокомментировать их.

![В файлах указаны группы [client], [client-mariadb], [mariadb], директивы шифрования данных (aria-encrypt-tables, encrypt-binlog, encrypt-tmp-disk-tables).](image/2.jpg){#fig:002 width=70%}

## Запуск и включение mariadb.

Запустить и включить программное обеспечение mariadb командами systemctl start mariadb и systemctl enable mariadb, убедиться, что mariadb прослушивает порт.

![Служба mariadb запущена и добавлена в автозапуск. Команда ss -tulpen | grep mysql показывает процесс mariadb, прослушивающий порт 3306.](image/3.jpg){#fig:003 width=70%}

## Настройка безопасности mariadb.

Запустить скрипт конфигурации безопасности mariadb командой mysql_secure_installation, установить пароль для пользователя root базы данных, отключить удалённый корневой доступ и удалить тестовую базу данных.

![В диалоге установлен пароль root, отключён удалённый корневой доступ, удалена тестовая база данных и анонимные пользователи, обновлены привилегии.](image/4.jpg){#fig:004 width=70%}

## Вход в MariaDB и список команд.

Войти в базу данных с правами администратора командой.

![В интерактивной оболочке MariaDB выведен список клиентских команд: charset, clear, connect, delimiter, edit, ego, exit.](image/5.jpg){#fig:005 width=70%}

## Просмотр доступных баз данных.

Из приглашения интерактивной оболочки MariaDB отобразить доступные в настоящее время базы данных запросом SHOW DATABASES

![Выведены базы данных: information_schema, mysql, performance_schema, sys.](image/6.jpg){#fig:006 width=70%}

## Статус MariaDB до настройки кодировки.

Для отображения статуса MariaDB ввести из приглашения интерактивной оболочки команду status и построчно пояснить выведенную информацию.

![Показаны Connection id, Current user root@localhost, Server version 10.11.18-MariaDB, Connection Localhost via UNIX socket, Server characterset latin1, Db characterset latin1.](image/7.jpg){#fig:007 width=70%}

## Создание файла utf8.cnf.

В каталоге /etc/my.cnf.d создать файл utf8.cnf и указать в нём конфигурацию для кодировки utf8.

![В файле прописаны секции [client] с default-character-set = utf8 и [mysqld] с character-set-server = utf8.](image/8.jpg){#fig:008 width=70%}

## Статус MariaDB после настройки кодировки.

Перезапустить MariaDB, войти в базу данных с правами администратора и посмотреть статус MariaDB, пояснить, что изменилось

![В статусе изменились Server characterset, Db characterset, Client characterset и Conn. characterset на utf8mb3. Connection id 3, Uptime 34 sec.](image/9.jpg){#fig:009 width=70%}

## Создание базы данных addressbook.

Создать базу данных с именем addressbook командой CREATE DATABASE addressbook CHARACTER SET utf8 COLLATE utf8_general_ci и перейти к ней командой USE addressbook.

![База данных создана, выполнено переключение на addressbook, команда SHOW TABLES показывает пустой набор.](image/10.jpg){#fig:010 width=70%}

## Создание таблицы city и заполнение данными.

Создать таблицу city с полями name и city, заполнить несколько строк данными (Иванов, Москва; Петров, Сочи; Сидоров, Дубна) и выполнить запрос SELECT * FROM city.

![Таблица создана, добавлены 3 строки, запрос SELECT выводит все записи таблицы.](image/11.jpg){#fig:011 width=70%}

## Создание пользователя и привилегий.

 Создать пользователя для работы с базой данных addressbook, задать пароль, предоставить права на действия с базой и посмотреть общую информацию о таблице city

![Выполнены CREATE USER vvustinova@'%', GRANT SELECT,NSERT, UPDATE, DELETE ON addressbook.*, FLUSH PRIVILEGES. Команда DESCRIBE city показывает поля name и city типа varchar(40)](image/12.jpg){#fig:012 width=70%}

## Просмотр баз и таблиц через mysqlshow

 Выйти из окружения MariaDB, просмотреть список баз данных командой mysqlshow -u root -p и список таблиц базы данных addressbook

![Выведены базы данных: addressbook, information_schema, mysql, performance_schema, sys. В базе addressbook присутствует таблица city.](image/13.jpg){#fig:013 width=70%}

## Резервное копирование базы данных.

Создать каталог для резервных копий, сделать резервную копию базы данных addressbook, сжатую резервную копию и сжатую копию с указанием даты

![Выполнены команды mysqldump с созданием файлов addressbook.sql, addressbook.sql.gz и addressbook.20261003.100018.sql.gz в каталоге /var/backup.](image/14.jpg){#fig:014 width=70%}

## Восстановление из резервной копии.

Восстановить базу данных addressbook из резервной копии и из сжатой резервной копии

![ Выполнены команды mysql -u root -p addressbook < /var/backup/addressbook.sql и zcat /var/backup/addressbook.sql.gz | mysql -u root -p addressbook.](image/15.jpg){#fig:015 width=70%}

## Копирование конфигурационных файлов MySQL.

На виртуальной машине server перейти в каталог /vagrant/provision/server, создать каталог mysql и скопировать в него конфигурационные файлы MariaDB и резервную копию базы данныx

![Созданы каталоги mysql/etc/my.cnf.d и mysql/var/backup, скопированы utf8.cnf и файлы резервных копий, создан исполняемый файл mysql.sh.](image/16.jpg){#fig:016 width=70%}

## Скрипт mysql.sh.

В каталоге /vagrant/provision/server создать исполняемый файл mysql.sh, который повторяет действия по установке и настройке сервера баз данных

![В скрипте прописаны перезапуск named, установка mariadb и mariadb-server, копирование конфигураций, запуск службы, безопасная установка и создание базы данных.](image/17.jpg){#fig:017 width=70%}

## Подключение provision в Vagrantfile.

Для отработки созданного скрипта во время загрузки виртуальных машин добавить в конфигурационном файле Vagrantfile в конфигурации сервера запись provision «server mysql».

![В Vagrantfile добавлены provision server dns, server dhcp, server http и server mysql с путями к соответствующим скриптам.](image/18.jpg){#fig:018 width=70%}


## Выводы

В ходе работы были приобретены практические навыки по установке и конфигурированию системы управления базами данных MariaDB. Освоены установка пакетов, настройка безопасности, изменение кодировки символов на utf8, создание базы данных addressbook и таблицы city, работа с пользователями и привилегиями. Выполнено резервное копирование и восстановление базы данных. Создан скрипт автоматической установки и настройки MariaDB для Vagrant.

## Библиографический список

1. MariaDB Foundation. — URL:
https://mariadb.org

2. Документация по MariaDB. — URL: https://mariadb.com/kb/ru/5306/

3. Основы языка SQL. — URL: http://citforum.ru/programming/321less/les44.shtml

4. MariaDB Documentation. Backing Up and Restoring. — URL: https://mariadb.com/kb/en/backup-and-restore-overview/

5. Red Hat Enterprise Linux 10. Configuring MariaDB. — URL: https://docs.redhat.com/en/documentation/red_hat_enterprise_linux/10/html/configuring_and_using_database_servers/

