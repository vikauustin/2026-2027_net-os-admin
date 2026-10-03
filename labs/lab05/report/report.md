---
## Front matter
title: "Лаборатораня работа №5"
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

Приобретение практических навыков по расширенному конфигурированию HTTP-сервера Apache в части безопасности и возможности использования PHP.

# Задание

1. Сгенерируйте криптографический ключ и самоподписанный сертификат безопасности для возможности перехода веб-сервера от работы через протокол HTTP
к работе через протокол HTTPS (см. раздел 5.4.1).
2. Настройте веб-сервер для работы с PHP (см. раздел 5.4.2).
3. Напишите (или скорректируйте) скрипт для Vagrant, фиксирующий действия по
расширенной настройке HTTP-сервера во внутреннем окружении виртуальной машины server (см. раздел 5.4.3).

# Выполнение лабораторной работы

В каталоге /etc/ssl создайте каталог private, сгенерируйте ключ и сертификат, далее требуется заполнить сертификат(рис. [-@fig:001]).

![Выполняем задания и команды](image/1.jpg){#fig:001 width=70%}

Переместить сгенерированный сертификат в каталог /etc/pki/tls/certs и проверить наличие файлов.(рис. [-@fig:002]).

![Выполнена команда mv www.vvustinova.net.crt /etc/pki/tls/certs, в каталоге /etc/ssl/certs/ присутствует www.vvustinova.net.crt, в /etc/ssl/private/ — www.vvustinova.net.key.](image/2.jpg){#fig:002 width=70%}

Открыть на редактирование файл /etc/httpd/conf.d/www.vvustinova.net.conf и заменить его содержимое, добавив настройки для работы через HTTPS и перенаправление с HTTP.(рис. [-@fig:003]).

![В файле прописаны два виртуальных хоста: на порту 80 с перенаправлением на HTTPS и на порту 443 с SSL. Указаны SSLCertificateFile и SSLCertificateKeyFile.](image/3.jpg){#fig:003 width=70%}

Настроить межсетевой экран для разрешения работы с https.(рис. [-@fig:004]).

![Выполнены команды firewall-cmd --add-service=https и firewall-cmd --add-service=https --permanent, затем firewall-cmd --reload.](image/4.jpg){#fig:004 width=70%}

На виртуальной машине client в браузере ввести название веб-сервера www.vvustinova.net и убедиться, что будет выведена страница с информацией об используемом на веб-сервере сервисе PHP.(рис. [-@fig:005]).

![ В браузере открыта страница https://www.vvustinova.net/ с сообщением Welcome to the www.vvustinova.net server](image/5.jpg){#fig:005 width=70%}

Просмотреть информацию о сертификате безопасности в браузере.(рис. [-@fig:006]).

![ В окне сертификата отображены Subject Name (Country RU, State Russia, Locality Moscow, Organization vvustinova, Common Name vvustinova.net), Issuer Name и Validity (Not Before Sat, 03 Oct 2026).](image/6.jpg){#fig:006 width=70%}

Создать файл index.php в каталоге веб-сервера с вызовом функции phpinfo() для проверки работы PHP.(рис. [-@fig:007]).

![ В файле записан код <?php phpinfo(); ?>.](image/7.jpg){#fig:007 width=70%}

Проверить работу PHP в браузере, открыв страницу www.vvustinova.net и убедившись, что отображается страница с информацией о PHP.(рис. [-@fig:008]).

![В браузере отобразилась страница PHP Version 8.3.33 с таблицей конфигурации.](image/8.jpg){#fig:008 width=70%}

 На виртуальной машине server перейти в каталог /vagrant/provision/server/http и скопировать конфигурационные файлы и сертификаты для дальнейшего использования в скрипте.(рис. [-@fig:009]).

![ Выполнено копирование файлов из /etc/httpd/conf.d/, /var/www/html/, созданы каталоги /etc/pki/tls/private и /etc/pki/tls/certs, скопированы ключ и сертификат.](image/9.jpg){#fig:009 width=70%}

 В файл /vagrant/provision/server/http.sh внести изменения, добавив установку PHP и настройку межсетевого экрана, разрешающую работать с https.(рис. [-@fig:010]).

![В скрипте прописаны установка Basic Web Server и php, копирование конфигураций, создание каталоговсимволической ссылки, установка прав, restorecon, настройка firewall для http и https.](image/10.jpg){#fig:010 width=70%}

# Выводы

В ходе работы были приобретены практические навыки по расширенному конфигурированию HTTP-сервера Apache. Сгенерирован самоподписанный сертификат и ключ безопасности, настроен переход веб-сервера на работу через протокол HTTPS, проверено перенаправление с HTTP. Настроена работа веб-сервера с PHP, проверена работоспособность через страницу phpinfo(). Скорректирован скрипт для Vagrant, фиксирующий действия по расширенной настройке HTTP-сервера.

# Ответы на контрольные вопросы

1. В чём отличие HTTP от HTTPS?

HTTPS — расширение протокола HTTP для поддержки шифрования в целях повышения безопасности. В отличие от HTTP, данные в HTTPS передаются в зашифрованном виде с использованием криптографических протоколов SSL или TLS.

2. Каким образом обеспечивается безопасность контента веб-сервера при работе через HTTPS?

Безопасность обеспечивается за счёт использования криптографических протоколов при организации HTTP-соединения и передачи по нему данных. Для шифрования применяется асимметричное шифрование для аутентификации, симметричное шифрование для конфиденциальности и коды аутентичности сообщений для сохранения целостности.

3. Что такое сертификационный центр? Приведите пример.

Сертификационный центр (Certification authority, CA) представляет собой компонент глобальной службы каталогов, отвечающий за управление криптографическими ключами пользователей. Его открытый ключ широко известен общественности и не вызывает сомнений в подлинности. Пример: Let's Encrypt.

# Библиографический список

1. Apache HTTP Server Version 2.4 Documentation. — URL: https://httpd.apache.org/docs/current/

2. mod_ssl — Apache HTTP Server. — URL: https://httpd.apache.org/docs/current/mod/mod_ssl.html

3. Rocky Linux Documentation. PHP and PHP-FPM. — URL: https://docs.rockylinux.org/10/guides/web/php/

4. Red Hat Enterprise Linux 10. Securing Networks. Hardening TLS Configuration in Applications. — URL: https://docs.redhat.com/en/documentation/red_hat_enterprise_linux/10/html/securing_networks/

5. SSL/TLS Strong Encryption: How-To. — URL: https://httpd.apache.org/docs/trunk/ssl/ssl_howto.html
