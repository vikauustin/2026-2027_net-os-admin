---
## Front matter
title: "лабораторная работа №7"
subtitle: "Расширенные настройки межсетевого экрана"
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

Получить навыки настройки межсетевого экрана в Linux в части переадресации
портов и настройки Masquerading.

# Задание

1. Настройте межсетевой экран виртуальной машины server для доступа к серверу
по протоколу SSH не через 22-й порт, а через порт 2022 (см. разделы 7.4.1 и 7.4.2).
2. Настройте Port Forwarding на виртуальной машине server (см. разделы 7.4.3).
3. Настройте маскарадинг на виртуальной машине server для организации доступа
клиента к сети Интернет (см. раздел 7.4.3).
4. Напишите скрипт для Vagrant, фиксирующий действия по расширенной настройке
межсетевого экрана. Соответствующим образом внести изменения в Vagrantfile
(см. раздел 7.4.4).

# Теоретическое введение

FirewallD — средство динамического управления межсетевым экраном в Rocky Linux, работающее как надстройка над Netfilter. Правилами управляют через утилиту `firewall-cmd`, а описания служб хранятся в XML-файлах: системные в `/usr/lib/firewalld/services`, пользовательские в `/etc/firewalld/services` [@fedora_firewalld_ru].

NAT — механизм преобразования IP-адресов транзитных пакетов. Маскарадинг — вид NAT, при котором адрес отправителя динамически заменяется адресом выходного интерфейса шлюза, что позволяет узлам внутренней сети выходить в Интернет через один внешний адрес. Port Forwarding позволяет перенаправлять трафик с одного порта на другой порт или узел [@cisco_nat_faq_ru].

# Выполнение лабораторной работы

На основе системного файла описания службы ssh создан файл `/etc/firewalld/services/ssh-custom.xml`, выполнен переход в каталог `/etc/firewalld/services` и выведено содержимое нового файла (рис. [-@fig:001]).

![Копирование файла описания службы ssh в ssh-custom.xml и просмотр его содержимого](image/1.jpg){#fig:001 width=70%}

Файл `ssh-custom.xml` открыт на редактирование: порт 22 заменён на 2022, имя службы изменено на SSH-custom, в описании указано, что это модифицированный файл службы (рис. [-@fig:002]).

![Отредактированный файл ssh-custom.xml с портом 2022](image/2.jpg){#fig:002 width=70%}

Выведен список доступных служб командой `firewall-cmd --get-services` (рис. [-@fig:003]).

![Список доступных служб: служба ssh-custom ещё не отображается](image/3.jpg){#fig:003 width=70%}

После перезагрузки правил межсетевого экрана повторно
выведены список доступных служб и список активных служб (рис. [-@fig:004]).

![Служба ssh-custom есть в списке доступных, но не активирована](image/4.jpg){#fig:004 width=70%}

Служба ssh-custom добавлена в FirewallD, затем добавлена в постоянную конфигурацию, и правила перезагружены (рис. [-@fig:005]).

![Служба ssh-custom добавлена в список активных служб](image/5.jpg){#fig:005 width=70%}

На сервере настроена переадресация с порта 2022 на порт 22. С клиента выполнено подключение по SSH к серверу через порт 2022 (рис. [-@fig:006]).

![Успешное подключение с клиента к серверу по SSH через порт 2022](image/6.jpg){#fig:006 width=70%}

На сервере проверено, активировано ли в ядре перенаправление IPv4-пакетов, командой `sysctl -a | grep forward` (рис. [-@fig:007]).

![Параметр net.ipv4.ip_forward равен 0, перенаправление выключено](image/7.jpg){#fig:007 width=70%}

Включено перенаправление IPv4-пакетов через файл `/etc/sysctl.d/90-forward.conf`, включён маскарадинг в зоне public, и правила перезагружены (рис. [-@fig:008]).

![Включение перенаправления IPv4-пакетов и маскарадинга на сервере](image/8.jpg){#fig:008 width=70%}

На клиенте проверена доступность сети Интернет командой `ping` (рис. [-@fig:009]).

![Клиент получает ответы от 8.8.8.8 и ya.ru без потерь пакетов](image/9.jpg){#fig:009 width=70%}

На сервере в каталоге `/vagrant/provision/server` создан каталог firewall, в соответствующие подкаталоги скопированы конфигурационные файлы, создан исполняемый файл `firewall.sh` (рис. [-@fig:010]).

![Создание каталогов, копирование файлов ssh-custom.xml и 90-forward.conf, создание firewall.sh](image/10.jpg){#fig:010 width=70%}

В файл `firewall.sh` записан скрипт, который копирует конфигурационные файлы, добавляет службу ssh-custom, переадресацию порта 2022 на 22 и маскарадинг, после чего перезагружает правила (рис. [-@fig:011]).

![Скрипт firewall.sh](image/11.jpg){#fig:011 width=70%}

Для отработки скрипта при загрузке виртуальной машины в Vagrantfile в конфигурацию сервера добавлена запись provision «server firewall» (рис. [-@fig:012]).

![Запись server firewall в Vagrantfile](image/12.jpg){#fig:012 width=70%}

# Выводы

В ходе работы получены навыки настройки межсетевого экрана в Linux в части переадресации портов и маскарадинга. Создана пользовательская служба ssh-custom, настроен доступ к серверу по SSH через порт 2022, включены перенаправление IPv4-пакетов и маскарадинг для выхода клиента в Интернет. Действия по настройке зафиксированы в скрипте `firewall.sh` и подключены в Vagrantfile.

# Ответы на контрольные вопросы

1. Где хранятся пользовательские файлы firewalld? — В каталоге `/etc/firewalld/`, описания служб в `/etc/firewalld/services/`.
2. Какую строку надо включить в пользовательский файл службы, чтобы указать порт TCP 2022? — `<port protocol="tcp" port="2022"/>`.
3. Какая команда позволяет перечислить все службы, доступные в настоящее время на сервере? — `firewall-cmd --get-services`.
4. В чём разница между NAT и маскарадингом? — NAT — общий механизм преобразования адресов, в том числе с заранее заданным внешним адресом. Маскарадинг — вид NAT, при котором адреса многих узлов динамически заменяются адресом выходного интерфейса шлюза с использованием номеров портов.
5. Какая команда разрешает входящий трафик на порт 4404 и перенаправляет его в службу ssh по IP-адресу 10.0.0.10? — `firewall-cmd --add-forward-port=port=4404:proto=tcp:toport=22:toaddr=10.0.0.10`.
6. Какая команда включает маскарадинг для всех пакетов, выходящих в зону public? — `firewall-cmd --zone=public --add-masquerade`.

# Список литературы{.unnumbered}

::: {#refs}
:::

