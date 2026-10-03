---
## Front matter
lang: ru-RU
title: Лабораторная работа №6
subtitle: Расширенная настройка HTTP-сервера Apache
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

Приобретение практических навыков по расширенному конфигурированию HTTP-сервера Apache в части безопасности и возможности использования PHP.

## Задание

1. Сгенерируйте криптографический ключ и самоподписанный сертификат безопасности для возможности перехода веб-сервера от работы через протокол HTTP
к работе через протокол HTTPS (см. раздел 5.4.1).
2. Настройте веб-сервер для работы с PHP (см. раздел 5.4.2).
3. Напишите (или скорректируйте) скрипт для Vagrant, фиксирующий действия по
расширенной настройке HTTP-сервера во внутреннем окружении виртуальной машины server (см. раздел 5.4.3).

## Генерация криптографического ключа и сертификата.

В каталоге /etc/ssl создайте каталог private, сгенерируйте ключ и сертификат, далее требуется заполнить сертификат

![Выполняем задания и команды](image/1.jpg){#fig:001 width=70%}

## Перемещение сертификата в каталог certs.

Переместить сгенерированный сертификат в каталог /etc/pki/tls/certs и проверить наличие файлов

![Выполнена команда mv www.vvustinova.net.crt /etc/pki/tls/certs, в каталоге /etc/ssl/certs/ присутствует www.vvustinova.net.crt, в /etc/ssl/private/ — www.vvustinova.net.key.](image/2.jpg){#fig:002 width=70%}

## Настройка виртуального хоста для HTTPS.

Открыть на редактирование файл /etc/httpd/conf.d/www.vvustinova.net.conf и заменить его содержимое, добавив настройки для работы через HTTPS и перенаправление с HTTP

![В файле прописаны два виртуальных хоста: на порту 80 с перенаправлением на HTTPS и на порту 443 с SSL. Указаны SSLCertificateFile и SSLCertificateKeyFile.](image/3.jpg){#fig:003 width=70%}

## Настройка межсетевого экрана для https.

Настроить межсетевой экран для разрешения работы с https.

![Выполнены команды firewall-cmd --add-service=https и firewall-cmd --add-service=https --permanent, затем firewall-cmd --reload.](image/4.jpg){#fig:004 width=70%}

## Проверка HTTPS-страницы в браузере.

На виртуальной машине client в браузере ввести название веб-сервера www.vvustinova.net и убедиться, что будет выведена страница с информацией об используемом на веб-сервере сервисе PHP

![ В браузере открыта страница https://www.vvustinova.net/ с сообщением Welcome to the www.vvustinova.net server](image/5.jpg){#fig:005 width=70%}

## Просмотр информации о сертификате.

Просмотреть информацию о сертификате безопасности в браузере

![ В окне сертификата отображены Subject Name (Country RU, State Russia, Locality Moscow, Organization vvustinova, Common Name vvustinova.net), Issuer Name и Validity (Not Before Sat, 03 Oct 2026).](image/6.jpg){#fig:006 width=70%}

## Создание файла index.php с phpinfo().

Создать файл index.php в каталоге веб-сервера с вызовом функции phpinfo() для проверки работы PHP

![ В файле записан код <?php phpinfo(); ?>.](image/7.jpg){#fig:007 width=70%}

## Проверка работы PHP в браузере.

Проверить работу PHP в браузере, открыв страницу www.vvustinova.net и убедившись, что отображается страница с информацией о PHP

![В браузере отобразилась страница PHP Version 8.3.33 с таблицей конфигурации.](image/8.jpg){#fig:008 width=70%}

## Копирование конфигурационных файлов и сертификатов.

 На виртуальной машине server перейти в каталог /vagrant/provision/server/http и скопировать конфигурационные файлы и сертификаты для дальнейшего использования в скрипте

![ Выполнено копирование файлов из /etc/httpd/conf.d/, /var/www/html/, созданы каталоги /etc/pki/tls/private и /etc/pki/tls/certs, скопированы ключ и сертификат.](image/9.jpg){#fig:009 width=70%}

## Скрипт http.sh с PHP и HTTPS.

 В файл /vagrant/provision/server/http.sh внести изменения, добавив установку PHP и настройку межсетевого экрана, разрешающую работать с https

![В скрипте прописаны установка Basic Web Server и php, копирование конфигураций, создание каталоговсимволической ссылки, установка прав, restorecon, настройка firewall для http и https.](image/10.jpg){#fig:010 width=70%}

## Выводы

В ходе работы были приобретены практические навыки по расширенному конфигурированию HTTP-сервера Apache. Сгенерирован самоподписанный сертификат и ключ безопасности, настроен переход веб-сервера на работу через протокол HTTPS, проверено перенаправление с HTTP. Настроена работа веб-сервера с PHP, проверена работоспособность через страницу phpinfo(). Скорректирован скрипт для Vagrant, фиксирующий действия по расширенной настройке HTTP-сервера.

## Библиографический список

1. Apache HTTP Server Version 2.4 Documentation. — URL: https://httpd.apache.org/docs/current/

2. mod_ssl — Apache HTTP Server. — URL: https://httpd.apache.org/docs/current/mod/mod_ssl.html

3. Rocky Linux Documentation. PHP and PHP-FPM. — URL: https://docs.rockylinux.org/10/guides/web/php/

4. Red Hat Enterprise Linux 10. Securing Networks. Hardening TLS Configuration in Applications. — URL: https://docs.redhat.com/en/documentation/red_hat_enterprise_linux/10/html/securing_networks/

5. SSL/TLS Strong Encryption: How-To. — URL: https://httpd.apache.org/docs/trunk/ssl/ssl_howto.html
