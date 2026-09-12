---
## Front matter
title: "Лаборатораня работа №2"
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

Приобретение практических навыков по установке и конфигурированию DNS-сервера, усвоение принципов работы системы доменных имён.

# Задание

1. Установите на виртуальной машине server DNS-сервер bind и bind-utils (см. раз-
дел 2.4.1).
2. Сконфигурируйте на виртуальной машине server кэширующий DNS-сервер (см.
раздел 2.4.2).
3. Сконфигурируйте на виртуальной машине server первичный DNS-сервер (см. раз-
дел 2.4.3).
4. При помощи утилит dig и host проанализируйте работу DNS-сервера (см. раз-
дел 2.4.4).
5. Напишите скрипт для Vagrant, фиксирующий действия по установке и конфигу-
рированию DNS-сервера во внутреннем окружении виртуальной машины server.
Соответствующим образом внесите изменения в Vagrantfile (см. раздел 2.4.5).

# Выполнение лабораторной работы

Запустите виртуальную машину server.На виртуальной машине server войдите под созданным вами в предыдущей работе
пользователем и откройте терминал.(рис. [-@fig:001]).

![Запускаем машину и подключаемся к ней через shh](image/1.jpg){#fig:001 width=70%}

В качестве упражнения с помощью утилиты dig сделайте запрос, например, к DNS адресу www.yandex.ru:(рис. [-@fig:002]).

![Выполнен запрос dig и видны ответы с ip- адресами](image/2.jpg){#fig:002 width=70%}

Проанализировать построчно содержание файлов /etc/resolv.conf, /etc/named.conf, /var/named/named.ca, /var/named/named.localhost, /var/named/named.loopback(рис. [-@fig:003]).

![Показано содержимое /etc/resolv.conf и /etc/named.conf с настройками DNS.](image/3.jpg){#fig:003 width=70%}

Запустить DNS-сервер командой systemctl start named и включить его автозапуск при загрузке системы командой systemctl enable named.(рис. [-@fig:004]).

![Просмотр каталога /var/named, запуск и включение службы named](image/4.jpg){#fig:004 width=70%}

 Проанализировать отличие в выводимой информации при выполнении команд dig www.yandex.ru и dig @127.0.0.1 www.yandex.ru.(рис. [-@fig:005]).

![Повторный запрос dig www.yandex.ru, ответ получен от DNS-сервера](image/5.jpg){#fig:005 width=70%}

Сделать DNS-сервер сервером по умолчанию для хоста server и внутренней виртуальной сети, изменив настройки соединения eth0 в NetworkManager.(рис. [-@fig:006]).

![Выполнен запрос dig @127.0.0.1 www.yandex.ru, получен ответ SERVFAIL.](image/6.jpg){#fig:006 width=70%}

Изменить настройки сетевого соединения eth0 через nmcli: удалить старые DNS, отключить автоматическое получение DNS и указать адрес 127.0.0.1..(рис. [-@fig:007]).

![В интерактивном редакторе nmcli настроены параметры DNS для eth0.](image/7.jpg){#fig:007 width=70%}

Перезапустить NetworkManager, проверить изменения в /etc/resolv.conf и открыть DNS в межсетевом экране командами firewall-cmd(рис. [-@fig:008]).

![Перезапуск NetworkManager, проверка resolv.conf и добавление службы DNS в firewall](image/8.jpg){#fig:008 width=70%}

Убедиться, что DNS-запросы идут через узел server, прослушивающий порт 53. Для этого используется команда lsof | grep UDP.(рис. [-@fig:009]).

![Вывод lsof показывает процессы named, слушающие UDP-порт 53.](image/9.jpg){#fig:009 width=70%}

 Скопировать шаблон описания DNS-зон named.rfc1912.zones из каталога /etc в каталог /etc/named и переименовать его в vvustinova.net(рис. [-@fig:010]).

![Выполнено копирование и переименование файла зон.](image/10.jpg){#fig:010 width=70%}

В каталоге /var/named создать подкаталоги master/fz и master/rz для файлов прямой и обратной зоны. Скопировать шаблон обратной зоны named.loopback в каталог master/rz.(рис. [-@fig:011]).

![Созданы каталоги, скопирован шаблон обратной зоны.](image/11.jpg){#fig:011 width=70%}

Изменить файл прямой зоны /var/named/master/fz/vvustinova.net, указав необходимые DNS-записи. Исправить права доступа к файлам в каталогах /etc/named и /var/named.(рис. [-@fig:012]).

![Редактирование файла зоны через nano и смена владельца на named:named](image/12.jpg){#fig:012 width=70%}

Скопировать шаблон прямой DNS-зоны named.localhost в каталог /var/named/master/fz и переименовать его в vvustinova.net(рис. [-@fig:013]).

![Созданы каталоги, скопирован и переименован шаблон прямой зоны.](image/13.jpg){#fig:013 width=70%}

Восстановить метки безопасности SELinux после изменения конфигурационных файлов named и дать разрешение на запись в файлы DNS-зоны.(рис. [-@fig:014]).

![Выполнены restorecon, проверка и установка переключателя named_write_master_zones.](image/14.jpg){#fig:014 width=70%}

Запустить в режиме реального времени расширенный лог системных сообщений journalctl -x -f и перезапустить DNS-сервер для проверки корректности работы.(рис. [-@fig:015]).

![В логе видно успешное завершение запуска службы named.service](image/15.jpg){#fig:015 width=70%}

Внести изменения в настройки внутреннего окружения виртуальной машины: создать скрипт dns.sh и добавить его вызов в Vagrantfile. Применить изменения командой vagrant reload server --provision.(рис. [-@fig:016]).

![Выполнен vagrant reload server --provision, настройки сервера применяются.](image/16.jpg){#fig:016 width=70%}

# Выводы

В ходе работы были приобретены практические навыки по установке и конфигурированию DNS-сервера bind. Освоены принципы работы системы доменных имён, настройка кэширующего и первичного DNS-сервера, создание прямой и обратной зон, а также работа с утилитами dig и host. Настроены права доступа, метки SELinux и правила межсетевого экрана. Создан скрипт автоматической настройки DNS-сервера.

# ответы на контрольные вопросы
1. Что такое DNS?

DNS (Domain Name System) — распределённая система, ставящая в соответствие доменному имени хоста IP-адрес и наоборот.

2. Каково назначение кэширующего DNS-сервера?

Кэширующий сервер получает рекурсивные запросы от клиентов и выполняет их с помощью нерекурсивных запросов к авторитативным серверам.

3. Чем отличается прямая DNS-зона от обратной?

Прямая зона ставит в соответствие доменному имени IP-адрес (записи A), обратная — IP-адрес доменному имени (записи PTR).

4. В каких каталогах и файлах располагаются настройки DNS-сервера?

Основные настройки: /etc/named.conf, /etc/named/, /var/named/, /etc/resolv.conf.

5. Что указывается в файле resolv.conf?

Адреса DNS-серверов, к которым обращается клиент, и параметры поиска доменов.

6. Какие типы записи описания ресурсов есть в DNS?

SOA, NS, A, PTR, CNAME, MX.

7. Для чего используется домен in-addr.arpa?

Для обратного преобразования IP-адресов в доменные имена.

8. Для чего нужен демон named?

Демон named обслуживает DNS-запросы, обеспечивая работу DNS-сервера BIND.

9. В чём заключаются основные функции slave-сервера и master-сервера?

Master-сервер загружает данные зоны из файла, slave-сервер получает данные зоны от master-сервера.

10. Какие параметры отвечают за время обновления зоны?

refresh, retry, expire, minimum в SOA-записи.

11. Как обеспечить защиту зоны от скачивания и просмотра?

Ограничить передачу зоны (allow-transfer), использовать TSIG-ключи, запретить рекурсию для внешних запросов.

12. Какая запись RR применяется при создании почтовых серверов?

MX-запись.

13. Как протестировать работу сервера доменных имён?

С помощью утилит dig и host, выполняя запросы к серверу.

14. Как запустить, перезапустить или остановить службу?

systemctl start, systemctl restart, systemctl stop.

15. Как посмотреть отладочную информацию при запуске сервиса?

journalctl -x -f или systemctl status.

16. Где храниться отладочная информация?

В журнале systemd, просмотр через journalctl.

17. Как посмотреть, какие файлы использует процесс?

lsof -p <PID> или lsof | grep <process>.

18. Примеры изменения сетевого соединения через nmcli.

nmcli connection edit eth0, nmcli connection modify, nmcli connection up.

19. Что такое SELinux?

Система принудительного контроля доступа в Linux.

20. Что такое контекст SELinux?

Метка безопасности, назначаемая процессам и файлам.

21. Как восстановить контекст SELinux?

Командой restorecon -vR <каталог>.

22. Как создать разрешающие правила из журналов?

С помощью audit2allow.

23. Что такое булевый переключатель в SELinux?

Параметр, включающий или отключающий определённые правила политики.

24. Как посмотреть список переключателей SELinux?

getsebool -a.

25. Как изменить значение переключателя SELinux?

setsebool <name> 1 илиsetsebool -P <name> 1.

Список литературы
Bart D. Common DNS Operational and Configuration Errors: RFC 1912. — 1996.

Security-Enhanced Linux. Руководство пользователя.

Systemd. — URL: https://wiki.archlinux.org/index.php/Systemd

Костромин В. А. Утилита lsof — инструмент администратора.

Поттеринг Л. Systemd для администраторов.

Сайт проекта NetworkManager.

Сайт проекта nmcli.

Названия слайдов для презентации
Установка DNS-сервера bind.

Проверка работы DNS через dig.

Анализ конфигурационных файлов DNS.

Запуск и автозапуск службы named.

Повторная проверка DNS-запроса.

Запрос к локальному DNS-серверу.

Настройка DNS через nmcli.

Перезапуск NetworkManager и настройка firewall.

Проверка прослушивания порта 53.

Копирование шаблона зон.

Создание каталогов прямой и обратной зоны.

Редактирование файла зоны и прав доступа.

Копирование шаблона прямой зоны.

Настройка SELinux для named.

Проверка работы через journalctl.

Применение настроек через Vagrant
