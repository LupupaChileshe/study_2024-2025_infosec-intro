---
## Front matter
lang: ru-RU
title: Работа с SELinux и веб-сервером Apache
subtitle: LAB №6
author:
  - Чилеше Лупупа
institute:
  - Российский университет дружбы народов, Москва, Россия
date: 29 сентября 2022 г.

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

  * Чилеше Лупупа
  * Студент
  * Российский университет дружбы народов
 

# SELinux и веб-сервер Apache: управление доступом и контексты безопасности

## Цель лабораторной работы

- Изучить SELinux в режиме enforcing с политикой targeted
- Понять, как SELinux управляет доступом к ресурсам Apache
- Научиться проверять, изменять и восстанавливать контексты безопасности
- Опробовать настройку политик SELinux для портов

## Что такое SELinux?

- Модуль безопасности Linux с системой обязательного контроля доступа (MAC)
- Использует метки (контексты безопасности) для ограничения доступа
- Защищает службы даже при наличии разрешений POSIX

## Начальная настройка

- Проверили статус SELinux командами getenforce, sestatus
- Проверили статус Apache: service httpd status
- Убедились, что процесс httpd работает с типом httpd_t

## статус SELinux

![](/home/lupupachileshe/work/study/2024-2025/Основы информационной безопасности/infosec-intro/labs/lab6/presentation/image/Screenshot from 2025-05-03 13-42-17.png)

## Работа с файловыми контекстами

- Проверили типы файлов в /var/www и /var/www/html
- Создали файл test.html с контекстом httpd_sys_content_t
- Убедились в доступности файла через браузер по адресу http://127.0.0.1/test.html

## типы файлов в /var/www и /var/www/html    

![](/home/lupupachileshe/work/study/2024-2025/Основы информационной безопасности/infosec-intro/labs/lab6/presentation/image/Screenshot from 2025-05-03 13-59-35.png)

## Разбор контекста SELinux

- Пример: unconfined_u:object_r:httpd_sys_content_t:s0
- Пользователь: unconfined_u
- Роль: object_r (не влияет на файлы)
- Тип: httpd_sys_content_t — разрешает доступ Apache

##контекста SELinux 

![](/home/lupupachileshe/work/study/2024-2025/Основы информационной безопасности/infosec-intro/labs/lab6/presentation/image/Screenshot from 2025-05-03 14-06-41.png)

## Эмуляция ошибки доступа

- Изменили тип контекста на samba_share_t: chcon -t samba_share_t test.html
- Попытка открыть файл → ошибка 403 Forbidden
- Причина найдена в логах: SELinux заблокировал доступ
 
## ошибки доступа

![](/home/lupupachileshe/work/study/2024-2025/Основы информационной безопасности/infosec-intro/labs/lab6/presentation/image/Screenshot from 2025-05-03 14-20-33.png)    
    
## Настройка порта

  Изменили порт Apache с 80 на 81 в httpd.conf
- Перезапуск Apache завершился с ошибкой
- Разрешили порт 81 для SELinux:

    semanage port -a -t http_port_t -p tcp 81

Apache успешно запустился и обслуживает файл на порту 81

## Очистка и восстановление

- Восстановили контекст файла: httpd_sys_content_t
- Вернули конфигурацию Apache на порт 80
- Удалили файл и удалили политику для порта 81

## Выводы

-  SELinux ограничивает доступ даже при разрешениях файловой системы
-  Для Apache важен корректный тип файлового контекста
-  Доступ к нестандартным портам нужно явно разрешать
-  Лог-файлы SELinux (особенно audit.log) — важный инструмент отладки


