---
## Front matter
title: "Лаборатораная работа №9"
subtitle: "Настройка POP3/IMAP сервера"
author: "Устинова Виктория Вадимовна"

## Generic otions
lang: ru-RU
toc-title: "Содержание"

## Bibliography
bibliography: bib/cite.bib
csl: pandoc/csl/gost-r-7-0-5-2008-numeric.csl
nocite: |
  @*

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

Приобретение практических навыков по установке и простейшему конфигурированию POP3/IMAP-сервера.

# Задание

1. Установите на виртуальной машине server Dovecot и Telnet для дальнейшей про-
верки корректности работы почтового сервера (см. раздел 9.4.1).
2. Настройте Dovecot (см. раздел 9.4.2).
3. Установите на виртуальной машине client программу для чтения почты Evolution
и настройте её для манипуляций с почтой вашего пользователя. Проверьте кор-
ректность работы почтового сервера как с виртуальной машины server, так
и с виртуальной машины client (см. раздел 9.4.3).
4. Измените скрипт для Vagrant, фиксирующий действия по установке и настройке
Postfix и Dovecote во внутреннем окружении виртуальной машины server, создайте
скрипт для Vagrant, фиксирующий действия по установке Evolution во внутрен-
нем окружении виртуальной машины client. Соответствующим образом внесите
изменения в Vagrantfile (см. раздел 9.4.4).

# Теоретическое введение

Dovecot — агент доставки почты (MDA) по протоколам POP3 и IMAP с возможностью обеспечения безопасности и надёжности за счёт использования протокола TLS.
Dovecot поддерживает основные форматы почтовых ящиков: mbox и Maildir
При использовании формата Maildir каждое сообщение хранится в отдельном файле
с уникальным именем, а каждая папка представляет собой каталог. Таким образом, не
требуется монопольный захват файла для обеспечения целостности почтового ящика
при чтении, добавлении или изменении сообщений. За блокировку отдельных файлов
сообщений отвечает файловая система.

# Выполнение лабораторной работы
 
В файле `dovecot.conf` прописан список протоколов `protocols = imap pop3`, в файле `10-auth.conf` проверен метод аутентификации plain. Затем выведен файл `auth-system.conf.ext`, в котором пароли проверяются драйвером pam (рис. [-@fig:001]).
 
![Настройка списка протоколов и проверка метода аутентификации и драйвера pam](image/1.jpg){#fig:001 width=70%}
 
В продолжении файла `auth-system.conf.ext` блок `passdb` с драйвером passwd закомментирован, поэтому пароли проверяются только через PAM (рис. [-@fig:002]).
 
![Продолжение файла auth-system.conf.ext: блок passdb с драйвером passwd закомментирован](image/2.jpg){#fig:002 width=70%}
 
В файле `10-mail.conf` задан формат Maildir в домашнем каталоге пользователя, а в Postfix указано `home_mailbox = Maildir/`. В межсетевом экране разрешены службы pop3, pop3s, imap и imaps, затем восстановлен контекст SELinux и перезапущен Postfix (рис. [-@fig:003]).
 
![Настройка расположения почтовых ящиков, каталога доставки Postfix и межсетевого экрана](image/3.jpg){#fig:003 width=70%}
 
Dovecot включён в автозапуск и запущен. Служба имеет статус active (running), версия Dovecot 2.3.21 (рис. [-@fig:004]).
 
![Запуск и проверка состояния службы Dovecot](image/4.jpg){#fig:004 width=70%}
 
С сервера отправлено письмо test1, оно появилось в каталоге Maildir (команда `mail`). Команда `doveadm mailbox list` показывает в списке ящиков пользователя INBOX (рис. [-@fig:005]).
 
![Отправка письма test1, просмотр почты в Maildir и список ящиков командой doveadm](image/5.jpg){#fig:005 width=70%}
 
На виртуальной машине client установлен почтовый клиент Evolution. При настройке учётной записи указаны имя Victoria и адрес `vvustinova@vvustinova.net` (рис. [-@fig:006]).
 
![Настройка учётной записи в Evolution: имя и адрес электронной почты](image/6.jpg){#fig:006 width=70%}
 
Для входящих сообщений выбран IMAP-сервер `mail.vvustinova.net`, порт 143, шифрование STARTTLS, аутентификация по паролю (рис. [-@fig:007]).
 
![Настройка получения почты в Evolution по протоколу IMAP](image/7.jpg){#fig:007 width=70%}
 
Для исходящих сообщений выбран SMTP-сервер `mail.vvustinova.net`, порт 25, без шифрования и аутентификации (рис. [-@fig:008]).
 
![Настройка отправки почты в Evolution по протоколу SMTP](image/8.jpg){#fig:008 width=70%}
 
Из Evolution отправлено письмо самому себе с темой test1 (рис. [-@fig:009]).
 
![Тестовое письмо, отправленное из Evolution](image/9.jpg){#fig:009 width=70%}
 
В журнале Dovecot фиксирует вход пользователя по IMAP с адреса клиента (метод PLAIN, TLS). Postfix принимает письмо по SMTP и доставляет его в Maildir со статусом `status=sent (delivered to maildir)` (рис. [-@fig:010]).
 
![Журнал maillog: вход по IMAP и доставка письма в Maildir](image/10.jpg){#fig:010 width=70%}
 
В ящике пользователя оказалось три письма: одно с сервера и два из Evolution. Затем через telnet выполнено подключение к `mail.vvustinova.net` на порт 110, сервер ответил `+OK Dovecot ready`, вход выполнен, введена команда `list` (рис. [-@fig:011]).
 
![Список писем в ящике и подключение по протоколу POP3 через telnet](image/11.jpg){#fig:011 width=70%}
 
В скрипт `mail.sh` сервера добавлена установка dovecot и telnet, копирование конфигурации из каталога provision и разрешение служб pop3 и pop3s в межсетевом экране. Остальные строки добавлены по заданию (рис. [-@fig:012]).
 
![Скрипт mail.sh виртуальной машины server](image/12.jpg){#fig:012 width=70%}
 
В скрипт `mail.sh` клиента добавлена установка почтового клиента Evolution (рис. [-@fig:013]).
 
![Скрипт mail.sh виртуальной машины client](image/13.jpg){#fig:013 width=70%}
 
# Выводы
 
В ходе работы получены практические навыки по установке и простейшему конфигурированию POP3/IMAP-сервера Dovecot. Dovecot настроен на работу по протоколам IMAP и POP3 с хранением почты в формате Maildir, а Postfix настроен на доставку писем в тот же каталог. С клиента через Evolution отправлены письма и получен доступ к ящику по IMAP, на сервере работа проверена командами mail, doveadm, анализом журнала и подключением по POP3 через telnet.

# Ответы на контрольные вопросы
 
1. За что отвечает протокол SMTP? — За отправку и передачу почты: от почтового клиента к серверу и между почтовыми серверами.

2. За что отвечает протокол IMAP? — За доступ к почте на сервере: письма остаются на сервере, клиент работает с ними и с папками удалённо, поэтому ящик можно использовать с нескольких устройств.

3. За что отвечает протокол POP3? — За получение почты с сервера: клиент обычно скачивает письма на своё устройство, после чего они могут быть удалены с сервера.

4. В чём назначение Dovecot? — Dovecot является агентом доставки почты (MDA): он хранит почтовые ящики и предоставляет пользователям доступ к ним по протоколам POP3 и IMAP, в том числе с защитой TLS.

5. В каких файлах обычно находятся настройки работы Dovecot? За что отвечает каждый из файлов? — `/etc/dovecot/dovecot.conf` — основной файл, в нём задаются протоколы и подключаются остальные файлы. В каталоге `/etc/dovecot/conf.d`: `10-auth.conf` — методы аутентификации, `auth-system.conf.ext` — источники паролей и пользователей (pam, passwd), `10-mail.conf` — расположение и формат почтовых ящиков, `10-ssl.conf` — параметры TLS и сертификаты, `10-master.conf` — службы и порты. Сертификаты находятся в каталоге `/etc/pki/dovecot`.

6. В чём назначение Postfix? — Postfix является агентом передачи почты (MTA): принимает почту от других серверов и от пользователей, доставляет её по назначению и передаёт в почтовые ящики.

7. Какие методы аутентификации пользователей можно использовать в Dovecot и в чём их отличие? — В Dovecot доступны, например, plain, login, cram-md5, digest-md5 и gssapi. При plain и login пароль передаётся в открытом виде (закодированном, но не зашифрованном), поэтому их следует использовать только вместе с TLS. Методы cram-md5 и digest-md5 основаны на обмене запросом и ответом и пароль по сети не передают, но требуют хранить пароли в определённом виде. Метод gssapi используется для единого входа с Kerberos.

8. Приведите пример заголовка письма с пояснениями его полей. — Например: `From: vvustinova@vvustinova.net` — отправитель; `To: vvustinova@server.vvustinova.net` — получатель; `Subject: test1` — тема; `Date: Sat, 17 Oct 2026 08:15:07 +0000` — дата и время отправки; `Message-Id: <...@server.vvustinova.net>` — уникальный идентификатор письма; `Return-Path` — адрес для возврата ошибок доставки; `Received` — служебная отметка каждого сервера, через который прошло письмо.

9. Приведите примеры использования команд для работы с почтовыми протоколами через терминал (например через telnet). — Подключение по POP3: `telnet mail.vvustinova.net 110`, затем `user имя`, `pass пароль`, `list` — список писем, `retr 1` — получить первое письмо, `dele 2` — удалить второе письмо, `quit` — завершить сеанс. Для SMTP подключаются к порту 25 и используют команды `helo`, `mail from:`, `rcpt to:`, `data`, `quit`.

10. Приведите примеры с пояснениями по работе с doveadm. — `doveadm mailbox list -u vvustinova` выводит список почтовых ящиков пользователя; `doveadm user vvustinova` показывает информацию о пользователе; `doveadm search -u vvustinova ALL` ищет все сообщения в ящике; `doveadm fetch -u vvustinova text mailbox INBOX` выводит содержимое сообщений; `doveadm expunge -u vvustinova mailbox INBOX ALL` удаляет сообщени

# Список литературы{.unnumbered}

::: {#refs}
:::
