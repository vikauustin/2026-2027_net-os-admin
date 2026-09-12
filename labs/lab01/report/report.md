---
## Front matter
title: "лабораторная работа №1"
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

Приобретение практических навыков установки Rocke Linux на виртуальную машину с помощью инструмента Vagrant

# Задание

1. Сформируйте box-файл с дистрибутивом Rocky Linux для VirtualBox (см. раздел 1.5.2
или 1.5.3).
2. Запустите виртуальные машины сервера и клиента и убедитесь в их работоспособ-
ности.
3. Внесите изменения в настройки загрузки образов виртуальных машин server
и client, добавив пользователя с правами администратора и изменив названия
хостов (см. раздел 1.5.4).
4. Скопируйте необходимые для работы с Vagrant файлы и box-файлы виртуальных
машин на внешний носитель. Используя эти файлы, вы можете попробовать раз-
вернуть виртуальные машины на другом компьютере.

# Выполнение лабораторной работы

Создать рабочий каталог для проекта vagrant и разместить в нем образ Rocke Linux HCL-файла makefileи подкаталоги http и scripts. Это подготавливает основу для сборки box-файла(рис. [-@fig:001]).

![В каталоге  находятся папки http, scripts и файлы Makefile, Rocky-10.2-x86_64-minimal.iso, vagrant-rocky.pkr.hc](image/1.jpg){#fig:001 width=70%}

Внести изменения в настройки внутреннего окружения машин, добавив пользователя с правами администратора через скрипт 01-user.sh. Скрипт создаёт пользователя, задаёт пароль и добавляет его в группу wheel(рис. [-@fig:002]).

![Скрипт создаёт пользователя vvustinova и добавляет его в группу wheel.](image/2.jpg){#fig:002 width=70%}

Сформировать box-файл с Rocky Linux для VirtualBox с помощью Packer. Выполняется инициализация плагинов и сборка образа; для QEMU сборка завершается ошибкой, для VirtualBox продолжается.(рис. [-@fig:003]).

![Запущена сборка box-файла через packer.exe; для QEMU — ошибка, для VirtualBox — процесс идёт.](image/3.jpg){#fig:003 width=70%}

Зарегистрировать созданный box-файл в Vagrant командой vagrant box add, чтобы его можно было использовать для запуска машин. После этого box-файл распаковывается и становится доступным(рис. [-@fig:004]).

![Выполнена команда vagrant box add rockylinux10, box-файл добавлен и распакован.](image/4.jpg){#fig:004 width=70%}

Запустить виртуальные машины сервера и клиента через провайдер VirtualBox. Перед запуском устанавливается плагин vagrant-vbguest, затем поднимаются обе машины.рис. [-@fig:005]).

![Установлен плагин vagrant-vbguest, затем запущены сервер и клиент.](image/5.jpg){#fig:005 width=70%}

Убедиться в работоспособности запущенных машин с помощью команды vagrant status. Обе машины должны находиться в состоянии running(рис. [-@fig:006]).

![vagrant status показывает, что server и client — в состоянии running](image/6.jpg){#fig:006 width=70%}

Подключиться к серверу и перейти под созданного пользователя командой su - vvustinova. После успешного входа приглашение меняется на vvustinovaserver.vvustinova.net(рис. [-@fig:007]).

![Выполнен вход su - vvustinova на сервере, приглашение сменилось на vvustinovaserver.vvustinova.net ](image/7.jpg){#fig:007 width=70%}

Подключиться к клиенту и перейти под созданного пользователя командой su - vvustinova. После успешного входа приглашение отображается как vvustinova@client.vvustinova.net(рис. [-@fig:008]).

![Выполнен вход su - vvustinova на клиенте, приглашение — vvustinova@client.vvustinova.net](image/8.jpg){#fig:008 width=70%}

Выключить машины и зафиксировать изменения внутренних настроек. Сначала выполняется vagrant halt, затем запуск сервера с --provision для применения скриптов.(рис. [-@fig:009]).

![Выполнены vagrant halt server и vagrant halt client, затем запущен vagrant up server --provision.](image/9.jpg){#fig:009 width=70%}

# Выводы

В ходе работы были приобретены практические навыки установки Rocky Linux на виртуальную машину с помощью Vagrant. Освоены основные команды инструмента, принципы работы с box-файлами, HCL-файлами и конфигурационным файлом Vagrantfile. Успешно сформирован box-файл, запущены виртуальные машины сервера и клиента, добавлен пользователь с правами администратора и настроены названия хостов.

# Контольные вопросы

1. Для чего предназначен Vagrant?

Vagrant — инструмент для создания и управления средами виртуальных машин в одном рабочем процессе. Он позволяет автоматизировать процесс установки на виртуальную машину как основного дистрибутива операционной системы, так и настройки необходимого программного обеспечения.

2. Что такое box-файл? В чём назначение Vagrantfile?

Box-файл (Vagrant Box) — сохранённый образ виртуальной машины с развёрнутой операционной системой, используемый как основа для клонирования виртуальных машин. Vagrantfile — конфигурационный файл на языке Ruby, в котором указаны настройки запуска виртуальной машины.

3. Приведите описание ипримеры вызова основных команд Vagrant.

vagrant help — вызов справки по командам;

vagrant box list — список подключённых box-файлов;

vagrant box add — подключение box-файла;

vagrant destroy — удаление box-файла из окружения;

vagrant init — создание шаблонного Vagrantfile;

vagrant up — запуск виртуальной машины;

vagrant reload — перезагрузка машины;

vagrant halt — остановка машины;

vagrant provision — настройка внутреннего окружения;

vagrant ssh — подключение к машине через ssh.

4. Дайте построчные пояснения содержания файлов vagrant-rocky.pkr.hcl, ks.cfg, Vagrantfile, Makefile.

vagrant-rocky.pkr.hcl — HCL-файл с метаданными по установке дистрибутива: версия, хэш, имя и пароль пользователя, настройки провайдеров VirtualBox и QEMU, скрипты провижининга.

ks.cfg — kickstart-файл, определяющий настройки установки: язык, клавиатура, таймзона, сеть, разметка диска, пользователи, пакеты.

Vagrantfile — конфигурация запуска машин server и client: провайдеры, объём памяти, CPU, сетевые интерфейсы, скрипты провижининга.

Makefile — набор инструкций для make по работе с Vagrant и Packer: сборка box-файла, запуск, остановка, провижининг, удаление машин.
