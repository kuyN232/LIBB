проанализировал предоставленные скрипты (3_M2.sh, 4_M2.sh, 5_M2(HQ-SRV (Moodle)).sh, 5_M2(BR-SRV (MediaWiki)).sh, 6_M2(HQ-SRV (Moodle)).sh, 8_M2.sh, 9_M2.sh, 10_M2.sh, Create 6_M2(BR-SRV (MediaWiki)).sh) и сравнил их с требованиями задания. Ниже приведены результаты анализа по каждому пункту задания Модуля 2, с указанием, какие требования выполнены, а какие нет, а также замечания.

Модуль 2: Организация сетевого администрирования
Задание 1: Доменный контроллер Samba на BR-SRV
Требования:

Настроить Samba как доменный контроллер на BR-SRV.
Создать 5 пользователей (user1.hq–user5.hq), группу hq, добавить пользователей в группу.
Ввести HQ-CLI в домен.
Пользователи группы hq могут аутентифицироваться на HQ-CLI.
Пользователи группы hq могут выполнять команды cat, grep, id с sudo, но не другие команды.
Импортировать пользователей из файла /opt/users.csv.
Проверка:

Скрипт: 4_M2.sh (назван как настройка Samba на HQ-SRV, но, вероятно, это ошибка в названии, так как должен быть для BR-SRV).
Анализ:
Скрипт устанавливает Samba и настраивает общую папку /srv/samba/share с пользователями alice и bob, но не настраивает Samba как доменный контроллер (нет samba-tool domain provision или аналогичных команд).
Пользователи user1.hq–user5.hq не создаются, группа hq не создаётся.
Нет настройки для ввода HQ-CLI в домен (например, через net ads join).
Нет настройки sudo для группы hq с ограничением на команды cat, grep, id.
Импорт из /opt/users.csv отсутствует.
Статус: Не выполнено. Скрипт настраивает Samba как файловый сервер, а не доменный контроллер, и не выполняет ни одного из требований задания.
Задание 2: Файловое хранилище
Требования:

На HQ-SRV создать RAID1 (md0) из трёх дисков (1 ГБ), файловая система ext4, автомонтирование в /raid1.
Настроить NFS-сервер, папка /raid1/nfs, доступ для чтения и записи для сети HQ-CLI.
На HQ-CLI настроить автомонтирование /raid1/nfs в /mnt/nfs.
Проверка:

Скрипты: Нет скрипта, связанного с настройкой RAID или NFS.
Анализ:
Ни один из предоставленных скриптов не содержит настройки RAID1 (mdadm), создания файловой системы ext4 или монтирования в /raid1.
Нет настройки NFS-сервера на HQ-SRV или автомонтирования на HQ-CLI.
Статус: Не выполнено.
Задание 3: Служба сетевого времени (chrony)
Требования:

Настроить сервер chrony на HQ-RTR (страта 5).
Настроить клиентов chrony на HQ-SRV, HQ-CLI, BR-RTR, BR-SRV.
Проверка:

Скрипт: 3_M2.sh.
Анализ:
HQ-RTR:
Скрипт настраивает chrony как сервер, если хост hq-rtr.au-team.irpo.
Указана страта 5 (local stratum 5).
Серверы NTP: 0.pool.ntp.org–3.pool.ntp.org.
Разрешён доступ для сетей 192.168.0.0/16 и 10.0.0.0/8, что не соответствует IP-адресам из задания (например, 172.16.x.x).
Клиенты (HQ-SRV, HQ-CLI, BR-RTR, BR-SRV):
Для других хостов настраивается клиент chrony с сервером hq-rtr.au-team.irpo.
Конфигурация корректна (driftfile, makestep, rtcsync).
Замечание: Сети в конфигурации сервера не соответствуют топологии (нужны 172.16.x.x).
Статус: Частично выполнено. Сервер и клиенты настроены, но неверные сети в конфигурации сервера.
Задание 4: Ansible на BR-SRV
Требования:

Настроить Ansible на BR-SRV, файл инвентаря в /etc/ansible, включает HQ-SRV, HQ-CLI, HQ-RTR, BR-RTR.
Все машины отвечают pong на ansible all -m ping.
Проверка:

Скрипт: 10_M2.sh.
Анализ:
Устанавливает Ansible через PPA.
Создаёт файл инвентаря /etc/ansible/hosts с группами:
servers: hq-srv.au-team.irpo, hq-cli.au-team.irpo, br-srv.au-team.irpo (пользователь sshuser).
routers: hq-rtr.au-team.irpo, br-rtr.au-team.irpo (пользователь net_admin).
Включает все требуемые машины.
Не содержит настройки SSH-ключей, но упоминает необходимость их настройки.
Нет проверки ansible ping, но структура инвентаря позволяет это выполнить при наличии SSH-доступа.
Статус: Выполнено, при условии ручной настройки SSH-ключей.
Задание 5: Docker на BR-SRV (MediaWiki)
Требования:

Создать файл wiki.yml в домашней директории пользователя для MediaWiki и MariaDB.
Сервис wiki (MediaWiki), монтирование LocalSettings.php.
Сервис mariadb, база mediawiki, пользователь wiki, пароль WikiP@ssw0rd.
Доступ через порт 8080.
Проверка:

Скрипты: 5_M2(BR-SRV (MediaWiki)).sh, Create 6_M2(BR-SRV (MediaWiki)).sh.
Анализ:
Оба скрипта идентичны, устанавливают Docker и запускают контейнеры MediaWiki и MySQL.
Контейнеры:
mediawiki: порт 8080:80, образ mediawiki:latest, база mediawiki, пользователь mediawiki, пароль wiki_pass (не соответствует WikiP@ssw0rd).
mediawiki_mysql: база mediawiki, пользователь mediawiki, пароль wiki_pass, образ mysql:5.7.
Несоответствия:
Нет файла wiki.yml (используются команды docker run, а не docker-compose).
Пароль базы (wiki_pass) не соответствует (WikiP@ssw0rd).
Нет монтирования LocalSettings.php.
Статус: Частично выполнено. MediaWiki развернут, но отсутствует wiki.yml, неверный пароль, нет монтирования LocalSettings.php.
Задание 6: Статическая трансляция портов
Требования:

BR-RTR: порт 80 → 8086 (BR-SRV, wiki).
HQ-RTR: порт 80 → 80 (HQ-SRV, moodle), порт 3010 → 3010 (HQ-SRV).
BR-RTR: порт 3010 → 3010 (BR-SRV).
Проверка:

Скрипты: Нет скриптов для настройки проброса портов на BR-RTR или HQ-RTR.
Анализ:
Ни один из скриптов не содержит настройки NAT через nftables или iptables для указанных портов.
Статус: Не выполнено.
Задание 7: Moodle на HQ-SRV
Требования:

Использовать Apache, MariaDB, база moodledb, пользователь moodle, пароль P@ssw0rd.
Пользователь admin с паролем P@ssw0rd.
Номер рабочего места на главной странице (арабская цифра).
Проверка:

Скрипты: 5_M2(HQ-SRV (Moodle)).sh, 6_M2(HQ-SRV (Moodle)).sh.
Анализ:
Оба скрипта идентичны, устанавливают Docker и запускают Moodle.
Контейнеры:
moodle: порт 80:80, образ moodlehq/moodle-php-apache:latest, база moodle, пользователь moodle, пароль moodlepass (не соответствует P@ssw0rd).
moodle_mysql: база moodle (не moodledb), пользователь moodle, пароль moodlepass, образ mysql:5.7.
Несоответствия:
Используется Docker, а не Apache/MariaDB непосредственно на хосте.
Название базы (moodle вместо moodledb), пароль (moodlepass вместо P@ssw0rd).
Нет настройки пользователя admin с паролем P@ssw0rd.
Нет настройки номера рабочего места на главной странице.
Статус: Частично выполнено. Moodle развернут, но не соответствует требованиям по базе, паролю, настройке admin и отображению номера.
Задание 8: Nginx как обратный прокси на ISP
Требования:

moodle.au-team.irpo → сервис moodle (HQ-SRV:80).
wiki.au-team.irpo → сервис MediaWiki (BR-SRV:8080).
Проверка:

Скрипт: 8_M2.sh.
Анализ:
Устанавливает Nginx и создаёт конфигурацию /etc/nginx/sites-available/proxy.conf.
Настройка:
moodle.au-team.irpo: прокси на hq-srv.au-team.irpo:80.
wiki.au-team.irpo: прокси на br-srv.au-team.irpo:8080.
Конфигурация корректна, активируется через символическую ссылку, Nginx перезапускается.
Статус: Выполнено.
Задание 9: Яндекс Браузер на HQ-CLI
Требования:

Установить Яндекс Браузер на HQ-CLI.
Проверка:

Скрипт: 9_M2.sh.
Анализ:
Устанавливает Яндекс Браузер через RPM-пакет.
Отключает NetworkManager, что не требуется, но не мешает выполнению.
Статус: Выполнено.
Итоговый статус выполнения Модуля 2
Выполнено полностью:
Задание 4 (Ansible на BR-SRV).
Задание 8 (Nginx как обратный прокси на ISP).
Задание 9 (Яндекс Браузер на HQ-CLI).
Частично выполнено:
Задание 3 (chrony, неверные сети в конфигурации сервера).
Задание 5 (MediaWiki, отсутствует wiki.yml, неверный пароль, нет монтирования LocalSettings.php).
Задание 7 (Moodle, неверные база и пароль, нет настройки admin и номера рабочего места).
Не выполнено:
Задание 1 (Samba как доменный контроллер).
Задание 2 (RAID1 и NFS).
Задание 6 (Статическая трансляция портов).
Замечания и рекомендации
Задание 1 (Samba):
Переименовать 4_M2.sh для BR-SRV и переписать для настройки доменного контроллера:
Использовать samba-tool domain provision.
Создать пользователей user1.hq–user5.hq, группу hq.
Настроить sudo для ограниченных команд.
Реализовать импорт из /opt/users.csv.
Настроить HQ-CLI для входа в домен.
Задание 2 (RAID1 и NFS):
Создать новый скрипт для HQ-SRV:
Настроить RAID1 с mdadm на трёх дисках, создать ext4, монтировать в /raid1.
Настроить NFS-сервер для /raid1/nfs.
Добавить в скрипт для HQ-CLI автомонтирование /mnt/nfs.
Задание 3 (chrony):
Исправить 3_M2.sh:
Заменить allow 192.168.0.0/16 и 10.0.0.0/8 на 172.16.0.0/16 для соответствия топологии.
Задание 5 (MediaWiki):
Переписать 5_M2(BR-SRV (MediaWiki)).sh:
Создать wiki.yml с docker-compose:
yaml

Свернуть

Перенос

Копировать
version: '3'
services:
  wiki:
    image: mediawiki:latest
    ports:
      - "8080:80"
    volumes:
      - ./LocalSettings.php:/var/www/html/LocalSettings.php
    environment:
      - MEDIAWIKI_DB_HOST=mariadb
      - MEDIAWIKI_DB_USER=wiki
      - MEDIAWIKI_DB_PASSWORD=WikiP@ssw0rd
      - MEDIAWIKI_DB_NAME=mediawiki
    depends_on:
      - mariadb
  mariadb:
    image: mariadb:latest
    environment:
      - MYSQL_DATABASE=mediawiki
      - MYSQL_USER=wiki
      - MYSQL_PASSWORD=WikiP@ssw0rd
      - MYSQL_ROOT_PASSWORD=rootpass
Исправить пароль на WikiP@ssw0rd.
Создать LocalSettings.php в домашней директории.
Задание 6 (Проброс портов):
Добавить скрипт для BR-RTR и HQ-RTR:
Использовать nftables для NAT:
BR-RTR: tcp dport 80 dnat to br-srv.au-team.irpo:8086, tcp dport 3010 dnat to br-srv.au-team.irpo:3010.
HQ-RTR: tcp dport 80 dnat to hq-srv.au-team.irpo:80, tcp dport 3010 dnat to hq-srv.au-team.irpo:3010.
Задание 7 (Moodle):
Переписать 5_M2(HQ-SRV (Moodle)).sh:
Установить Apache и MariaDB на хосте, а не в Docker.
Создать базу moodledb, пользователя moodle с паролем P@ssw0rd.
Настроить Moodle с пользователем admin и паролем P@ssw0rd.
Добавить номер рабочего места через настройку темы Moodle.
Пример исправленного скрипта для Задания 6 (BR-RTR)
bash

Свернуть

Перенос

Исполнить

Копировать
#!/bin/bash
# Настройка проброса портов на BR-RTR

cat <<EOF > /etc/nftables/nftables.nft
#!/usr/sbin/nft -f
flush ruleset

table ip nat {
    chain prerouting {
        type nat hook prerouting priority 0; policy accept;
        tcp dport 80 dnat to 172.16.1.2:8086
        tcp dport 3010 dnat to 172.16.1.2:3010
    }
    chain postrouting {
        type nat hook postrouting priority 0; policy accept;
        ip saddr 172.16.1.0/28 oifname "ens192" counter masquerade
    }
}
EOF

nft -f /etc/nftables/nftables.nft
systemctl enable --now nftables
echo "Проброс портов настроен на BR-RTR."
Если требуется помощь с конкретными исправлениями или полными скриптами для невыполненных заданий, уточните, и я предоставлю код.
