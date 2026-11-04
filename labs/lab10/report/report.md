---
## Front matter
title: "Лабораторная работа №10"
subtitle: "Расширенные настройки SMTP-сервера"
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

Приобретение практических навыков по конфигурированию SMTP-сервера в части настройки аутентификации

# Задание

1. Настройте Dovecot для работы с LMTP (см. раздел 10.4.1).
2. Настройте аутентификацию посредством SASL на SMTP-сервере (см. раздел 10.4.2).
3. Настройте работу SMTP-сервера поверх TLS (см. раздел 10.4.3).
4. Скорректируйте скрипт для Vagrant, фиксирующий действия расширенной на-
стройки SMTP-сервера во внутреннем окружении виртуальной машины server (см.
раздел 10.4.4).

# Теоретическое введение

Local Mail Transfer Protocol (LMTP) — протокол локальной пересылки почты.
По сути, Dovecot с включённым в него функционалом LMTP выступает в качестве локального агента доставки почты, т.е. является службой приёма почтовых сообщений от
SMTP-сервера для последующей их пересылки клиентам локальной сети. Использование Dovecot и протокола LMTP позволяет организовать фильтрацию почты на стороне
сервера в момент размещения письма в почтовый ящик, а не на стороне клиента.

Simple Authentication and Security Layer (SASL) — механизм обеспечения аутентификации и идентификации пользователей, а также защиты данных в протоколах,
ориентированных на соединение (например, в IMAP, POP, SMTP, LDAP, telnet, FTP и т.п.).
SASL, по сути, является посредником (промежуточным слоем) между каким-то приложением и взаимодействующим с ним протоколом, добавляя к протоколу команды
идентификации и аутентификации пользователя, а также определяя протокол обеспечения безопасности (шифрования)

# Выполнение лабораторной работы
 
В список протоколов Dovecot добавлен протокол LMTP (`protocols = imap pop3 lmtp`). В файл `10-master.conf` добавлено определение сервиса lmtp: unix-сокет `dovecot-lmtp` в каталоге Postfix с владельцем postfix и правами 0600, чтобы только Postfix мог передавать в него письма. Также определена служба аутентификации `service auth`: сокет `private/auth` с правами 0660 для Postfix используется для проверки паролей через Dovecot, а сокет `auth-userdb` с правами 0600 доступен только пользователю dovecot (рис. [-@fig:001]).
 
![Добавление протокола lmtp и определение сервисов lmtp и auth в 10-master.conf](image/1.jpg){#fig:001 width=70%}
 
В Postfix передача сообщений настроена через сокет Dovecot (`mailbox_transport = lmtp:unix:private/dovecot-lmtp`). В `10-auth.conf` задан формат имени пользователя без домена `auth_username_format = %Ln` и разрешена передача пароля открытым текстом. После перезапуска Postfix и Dovecot служба Dovecot имеет статус active (running) (рис. [-@fig:002]).
 
![Настройка передачи почты через LMTP, формата имени пользователя и перезапуск служб](image/2.jpg){#fig:002 width=70%}
 
С клиента отправлено письмо «LMTP test». В журнале видно, что Dovecot принял соединение от Postfix через сервис lmtp и сохранил письмо в INBOX пользователя vvustinova. Postfix при этом передал письмо через unix-сокет `private/dovecot-lmtp` со статусом `status=sent`, после чего удалил его из очереди (рис. [-@fig:003]).
 
![Журнал maillog: письмо принято через LMTP и сохранено в INBOX](image/3.jpg){#fig:003 width=70%}
 
Почтовый ящик пользователя на сервере просмотрен командой `MAIL=~/Maildir mail`. Отправленное с клиента письмо «LMTP test» находится в ящике и помечено как новое (рис. [-@fig:004]).
 
![Письмо LMTP test в почтовом ящике пользователя на сервере](image/4.jpg){#fig:004 width=70%}
 
Для Postfix заданы тип аутентификации SASL `dovecot` и путь к сокету `private/auth`. Параметр `smtpd_recipient_restrictions` задаёт порядок проверки получателя: отклоняются домены без DNS-записи и адреса не в полной форме, разрешается отправка из доверенной сети, запрещается пересылка на чужие домены (защита от использования сервера как relay), отклоняются несуществующие получатели, остальное разрешается. Параметр `mynetworks` ограничен локальным адресом `127.0.0.0/8` (рис. [-@fig:005]).
 
![Настройка SASL-аутентификации и ограничений получателей в Postfix](image/5.jpg){#fig:005 width=70%}
 
Для проверки аутентификации в `master.cf` для порта 25 временно добавлены опции `smtpd_sasl_auth_enable=yes` и ограничения получателей, разрешающие отправку аутентифицированным пользователям (рис. [-@fig:006]).
 
![Временная настройка master.cf: SASL-аутентификация на порту 25](image/6.jpg){#fig:006 width=70%}
 
На клиенте получена строка для аутентификации в формате base64, после чего выполнено подключение к `server.vvustinova.net` на порт 25 через telnet. Сервер ответил приветствием Postfix, в ответе на `EHLO` указан механизм `AUTH PLAIN`. Команда `AUTH PLAIN` с полученной строкой завершилась ответом `235 2.7.0 Authentication successful`, то есть аутентификация прошла (рис. [-@fig:007]).
 
![Проверка SMTP-аутентификации на клиенте через telnet](image/7.jpg){#fig:007 width=70%}
 
Для работы SMTP-сервера поверх TLS в Postfix указаны сертификат и ключ Dovecot, каталог для хранения TLS-сессий и уровень безопасности `may`. В `master.cf` для порта 25 оставлена обычная запись smtpd, а для порта 587 добавлена служба submission с обязательным шифрованием, SASL-аутентификацией и ограничениями получателей. В межсетевом экране разрешена служба smtp-submission (рис. [-@fig:008]).
 
![Настройка TLS, службы submission в master.cf и межсетевого экрана](image/8.jpg){#fig:008 width=70%}
 
В почтовом клиенте Evolution изменены настройки отправки: порт 587, включена аутентификация сервера, шифрование STARTTLS, тип аутентификации PLAIN, пользователь vvustinova (рис. [-@fig:009]).
 
![Настройка SMTP в Evolution: порт 587, STARTTLS и аутентификация по паролю](image/9.jpg){#fig:009 width=70%}
 
В журнале сервера видно, что клиент
подключился с аутентификацией (`sasl_method=PLAIN`, `sasl_username=vvustinova`) и использовал STARTTLS. Письмо принято Postfix, передано в транспорт lmtp и сохранено Dovecot в INBOX, то есть отправка из Evolution работает (рис. [-@fig:010]).
 
![Журнал maillog: отправка из Evolution с аутентификацией и доставка через LMTP](image/10.jpg){#fig:010 width=70%}
 
В скрипт `mail.sh` сервера внесены изменения по расширенной настройке: передача почты через LMTP, SASL-аутентификация, ограничения получателей, `mynetworks` и настройка TLS с копированием сертификата и ключа Dovecot (рис. [-@fig:011]).
 
![Фрагмент скрипта mail.sh виртуальной машины server](image/11.jpg){#fig:011 width=70%}
 
В скрипт `mail.sh` клиента добавлена установка telnet (рис. [-@fig:012]).
 
![Скрипт mail.sh виртуальной машины client](image/12.jpg){#fig:012 width=70%}
 
# Выводы
 
В ходе работы получены практические навыки по настройке аутентификации на SMTP-сервере. Dovecot настроен для работы с LMTP, а Postfix передаёт ему почту через unix-сокет. Настроена SASL-аутентификация через Dovecot, ограничен приём почты только для обслуживаемых получателей и настроена работа SMTP-сервера поверх TLS на порту 587. Отправка почты с аутентификацией проверена через telnet и через Evolution, изменения зафиксированы в скриптах `mail.sh` для сервера и клиента.
 
# Ответы на контрольные вопросы
 
1. Приведите пример задания формата аутентификации пользователя в Dovecot в форме логина с указанием домена. — В файле `/etc/dovecot/conf.d/10-auth.conf` задаётся `auth_username_format = %Lu`: логин в нижнем регистре с доменом (например, `vvustinova@vvustinova.net`). Для логина без домена используется `%Ln`.

2. Какие функции выполняет почтовый Relay-сервер? — Relay-сервер принимает почту от клиентов или других серверов и пересылает её дальше к серверу получателя. Он может обслуживать локальную сеть как единый почтовый шлюз, проверять отправителей и фильтровать письма.

3. Какие угрозы безопасности могут возникнуть в случае настройки почтового сервера как Relay-сервера? — Если ретрансляция разрешена всем без проверки (open relay), сервер можно использовать для рассылки спама. Это приводит к занесению адреса и домена в чёрные списки, перегрузке сервера и потере репутации, а также к возможной подмене отправителя. Для защиты используются аутентификация пользователей и ограничения получателей, такие как `reject_unauth_destination`.

# Список литературы{.unnumbered}
 
::: {#refs}
:::
