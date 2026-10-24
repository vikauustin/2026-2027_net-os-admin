---
## Front matter
title: "Лабораторная работа №8"
subtitle: "Настройка SMTP-сервера"
author: "Устинова Виктория Вадимовна"

## Generic otions
lang: ru-RU
toc-title: "Содержание"

## Bibliography
bibliography: bib/cite.bib
csl: pandoc/csl/gost-r-7-0-5-2008-numeric.csl
notice: |
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

Приобретение практических навыков по установке и конфигурированию SMTP-сервера.

# Задание

1. Установите на виртуальной машине server SMTP-сервер postfix (см. раздел 8.4.1).
2. Сделайте первоначальную настройку postfix при помощи утилиты postconf, задав
отправку писем не на локальный хост, а на сервер в домене (см. раздел 8.4.2).
3. Проверьте отправку почты с сервера и клиента (см. раздел 8.4.3).
4. Сконфигурируйте Postfix для работы в домене. Проверьте отправку почты с сервера
и клиента (см. раздел 8.4.4).
5. Напишите скрипт для Vagrant, фиксирующий действия по установке и настройке
Postfix во внутреннем окружении виртуальной машины server. Соответствующим
образом внесите изменения в Vagrantfile (см. раздел 8.4.5).

# Теоретическое введение
 
Postfix — агент передачи почты. Он принимает почту от других серверов и от аутентифицированных пользователей, доставляет её по назначению и обрабатывает почту, адресованную локальным службам сервера. Настройки хранятся в каталоге `/etc/postfix`, основной файл конфигурации — `main.cf`, в котором задаются имя узла `myhostname`, домен `mydomain`, домен отправителя `myorigin`, список адресатов `mydestination`, интерфейсы `inet_interfaces`, протоколы `inet_protocols` и доверенные сети `mynetworks`. Менять параметры удобно утилитой `postconf` [@postfix_docs].
 
Для работы с почтой из консоли используется клиент mail, который в Rocky Linux входит в пакет s-nail. Журнал почтовой службы находится в файле `/var/log/maillog`, а очередь сообщений просматривается командой `postqueue -p`.
 
# Выполнение лабораторной работы
 
На виртуальной машине server под суперпользователем установлены пакеты postfix и s-nail. На скриншоте видно завершение установки postfix и установку s-nail из репозитория appstream (рис. [-@fig:001]).
 
![Установка пакетов postfix и s-nail на виртуальной машине server](image/1.jpg){#fig:001 width=70%}
 
Межсетевой экран сконфигурирован так, чтобы пропускать службу SMTP: служба добавлена в текущую и в постоянную конфигурацию, в списке служб появилась smtp. Затем восстановлен контекст безопасности SELinux командой `restorecon -vR /etc`, после чего Postfix включён в автозапуск и запущен (рис. [-@fig:002]).
 
![Разрешение службы smtp в межсетевом экране, восстановление контекста SELinux, запуск Postfix](image/2.jpg){#fig:002 width=70%}
 
Для первоначальной настройки просмотрены значения параметров: `myorigin = $myhostname`, `mydomain = vvustinova.net`(рис. [-@fig:003]).
 
![Просмотр параметров myorigin и mydomain и замена myorigin на $mydomain](image/3.jpg){#fig:003 width=70%}
 
Корректность файла main.cf проверена командой `postfix check`, конфигурация перечитана командой `systemctl reload postfix`. Командой `postconf -n` выведены параметры, значения которых отличаются от значений по умолчанию (рис. [-@fig:004]).
 
![Проверка конфигурации, перезагрузка Postfix и вывод изменённых параметров](image/4.jpg){#fig:004 width=70%}
 
В конце вывода `postconf -n` виден параметр `myorigin = $mydomain`. Затем значение домена задано жёстко: `mydomain = vvustinova.net`. Параметр `inet_protocols` имел значение all, поэтому он заменён на ipv4, чтобы отключить IPv6, после чего конфигурация проверена и перезагружена (рис. [-@fig:005]).
 
![Жёсткое задание домена и отключение IPv6 в параметре inet_protocols](image/5.jpg){#fig:005 width=70%}
 
В журнале видно, что письмо принято (pickup), поставлено в очередь (qmgr) и передано локальной доставке со статусом `status=sent`, после чего удалено из очереди (removed). Значит, сообщение доставлено. Также в журнале есть предупреждение о том, что новое значение inet_protocols будет применено только после полной остановки и запуска Postfix (рис. [-@fig:006]).
 
![Журнал maillog: письмо test1 доставлено в почтовый ящик](image/6.jpg){#fig:006 width=70%}
 
Проверено содержимое каталога `/var/spool/mail`: в нём появился файл пользователя vvustinova, а в самом файле находится отправленное письмо с темой test1. В заголовках указан отправитель `vvustinova@vvustinova.net`, то есть параметр myorigin применился (рис. [-@fig:007]).
 
![Каталог /var/spool/mail и содержимое почтового ящика пользователя](image/7.jpg){#fig:007 width=70%}
 
на клиенте отключён IPv6 в параметре inet_protocols, Postfix включён в автозапуск и запущен. С клиента отправлено письмо test2 на адрес. Команда `postqueue -p` показывает, что письмо осталось в очереди с ошибкой `Connection refused` при подключении к серверу на порт 25, то есть доставлено не было (рис. [-@fig:008]).
 
![Настройка Postfix на клиенте и письмо test2, застрявшее в очереди](image/8.jpg){#fig:008 width=70%}
 
Причина отказа в соединении видна из настроек сервера: параметр `inet_interfaces = localhost`, то есть Postfix принимает соединения только с локального узла, а `mynetworks = 127.0.0.0/32`. Поэтому на сервере значение `inet_interfaces` заменено на all, в `mynetworks` добавлена внутренняя сеть `127.0.0.0/8, 192.168.0.0/16`. (рис. [-@fig:009]).
 
![Разрешение Postfix принимать соединения со всех интерфейсов и из внутренней сети](image/9.jpg){#fig:009 width=70%}
 
После изменения настроек сообщение с клиента отправлено повторно. В журнале сервера видно подключение клиента `client.vvustinova.net[192.168.1.30]`, приём сообщения демоном smtpd и локальную доставку со статусом `status=sent`. Значит, теперь почта с клиента на сервер доставляется (рис. [-@fig:010]).
 
![Журнал сервера: письмо с клиента принято и доставлено в почтовый ящик](image/10.jpg){#fig:010 width=70%}
 
Далее настроена работа в домене. С клиента отправлено письмо test3 на доменный адрес `vvustinova@vvustinova.net`. Команда `postqueue -p` по-прежнему показывает в очереди клиента прежнее недоставленное сообщение с ошибкой подключения (рис. [-@fig:011]).
 
![Отправка письма test3 с клиента на доменный адрес и просмотр очереди](image/11.jpg){#fig:011 width=70%}
 
В журнале сервера видно, что письмо на доменный адрес не доставлено: статус `status=bounced (mail for vvustinova.net loops back to myself)`. Сервер не считает домен vvustinova.net конечной точкой доставки, и сообщение возвращается. Уведомление об ошибке отправителю тоже не ушло: подключение к клиенту завершилось ошибкой `No route to host`, статус deferred (рис. [-@fig:012]).
 
![Журнал сервера: письмо на доменный адрес вернулось с ошибкой loops back to myself](image/12.jpg){#fig:012 width=70%}
 
Для отправки сообщений на доменный адрес в файл прямой DNS-зоны `/var/named/master/fz/vvustinova.net` добавлена MX-запись `MX 10 mail.vvustinova.net.` и запись `mail A 192.168.1.1` (рис. [-@fig:013]).
 
![Файл прямой DNS-зоны с MX-записью и записью mail](image/13.jpg){#fig:013 width=70%}
 
В файл обратной DNS-зоны `/var/named/master/rz/192.168.1` добавлена запись `1 PTR mail.vvustinova.net.` (рис. [-@fig:014]).
 
![Файл обратной DNS-зоны с PTR-записью для mail](image/14.jpg){#fig:014 width=70%}
 
перезагружена конфигурация Postfix, восстановлены контексты безопасности SELinux, перезапущен DNS и отправлены сообщения из очереди. Журнал сервера показывает, что письмо для адреса `vvustinova@vvustinova.net` принято и доставлено в почтовый ящик со статусом `status=sent (рис. [-@fig:015]).
 
![Журнал сервера: письмо на доменный адрес доставлено](image/15.jpg){#fig:015 width=70%}

На сервере в каталоге `/vagrant/provision/server` создан скрипт `mail.sh`. Он устанавливает postfix и s-nail, разрешает службу smtp в межсетевом экране, восстанавливает контексты SELinux,включает и запускает Postfix, а затем задаёт параметры mydomain, myorigin, inet_protocols, inet_interfaces, mydestination и mynetworks (рис. [-@fig:016]).
 
![Скрипт mail.sh для виртуальной машины server](image/16.jpg){#fig:016 width=70%}
 
На клиенте в каталоге `/vagrant/provision/client` создан скрипт `mail.sh`, который устанавливает postfix и s-nail, отключает IPv6 в параметре inet_protocols, включает и запускает Postfix (рис. [-@fig:017]).
 
![Скрипт mail.sh для виртуальной машины client](image/17.jpg){#fig:017 width=70%}
 
Для отработки скриптов при загрузке виртуальных машин в Vagrantfile в конфигурацию сервера добавлена запись provision «server mail» (рис. [-@fig:018]).
 
![Запись server mail в Vagrantfile](image/18.jpg){#fig:018 width=70%}
 
В конфигурацию клиента добавлена запись provision «client mail» (рис. [-@fig:019]).
 
![Запись client mail в Vagrantfile](image/19.jpg){#fig:019 width=70%}
 
# Выводы
 
В ходе работы получены практические навыки по установке и конфигурированию SMTP-сервера Postfix. Выполнена первоначальная настройка параметров с помощью postconf, проверена отправка почты с сервера и клиента. Для приёма почты от клиента потребовалось разрешить Postfix слушать все интерфейсы и добавить внутреннюю сеть в mynetworks, а для работы с доменным адресом — добавить в DNS MX-запись и указать домен в mydestination. Действия по настройке зафиксированы в скриптах `mail.sh` для сервера и клиента, которые подключены в Vagrantfile.
 
# Ответы на контрольные вопросы
 
1. В каком каталоге и в каком файле следует смотреть конфигурацию Postfix? — В каталоге `/etc/postfix`, основной файл конфигурации — `main.cf`.

2. Каким образом можно проверить корректность синтаксиса в конфигурационном файле Postfix? — Командой `postfix check`.

3. В каких параметрах конфигурации Postfix требуется внести изменения для настройки возможности отправки писем не на локальный хост, а на доменные адреса? — Нужно задать `mydomain`, указать `myorigin = $mydomain` и добавить `$mydomain` в параметр `mydestination`. Кроме того, для работы в сети нужны `inet_interfaces = all` и `mynetworks` с адресом внутренней сети, а в DNS — MX-запись для почтового сервера.

4. Приведите примеры работы с утилитой mail по отправке письма, просмотру имеющихся писем, удалению письма. — Отправка: `echo "текст" | mail -s "тема" user@domain`. Просмотр: команда `mail` без параметров выводит список писем, `h n` показывает заголовок письма с номером n, `n` переходит к следующему письму. Удаление: `d` удаляет письмо, `dq` удаляет письмо и завершает работу mail. Проверить наличие почты можно командой `mail -e`.

5. Приведите примеры работы с утилитой postqueue. Как посмотреть очередь сообщений? Как определить число сообщений в очереди? Как отправить все сообщения, находящиеся в очереди? Как удалить письмо из очереди? — Очередь выводится командой `postqueue -p`, в последней строке вывода указано число сообщений (Request). Все сообщения из очереди отправляются командой `postqueue -f`. Письмо удаляется командой `postsuper -d идентификатор_письма`, а вся очередь — командой `postsuper -d ALL`.

# Список литературы{.unnumbered}
 
::: {#refs}
:::
