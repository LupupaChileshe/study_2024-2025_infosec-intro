---
## Front matter
lang: ru-RU
title: Структура научной презентации
subtitle: Простейший шаблон
author:
  - Чилеше Л.
institute:
  - Российский университет дружбы народов, Москва, Россия
  - Объединённый институт ядерных исследований, Дубна, Россия
date: 01 января 1970

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
 - '\makeatletter'
 - '\beamer@ignorenonframefalse'
 - '\makeatother'
---

# Информация

## Докладчик

:::::::::::::: {.columns align=center}
::: {.column width="70%"}

  * Чилеше Л.
  * Студент
  * Российский университет дружбы народов

# Идентификаторы пользователя и группы, биты SetUID, SetGID и Sticky

## Цель лабораторной работы:

Изучить механизм управления правами доступа в операционной системе Linux на уровне идентификаторов пользователя (UID) и группы (GID), а также на практике освоить работу с бітами SetUID, SetGID и Sticky, их влияние на выполнение программ и управление файлами.
Научиться использовать системные вызовы для получения реальных и эффективных UID/GID, изменять права доступа и анализировать безопасность при совместной работе пользователей.



# Создание программы

## Вход в систему от имени guest

![](/home/lupupachileshe/work/study/2024-2025/Основы информационной безопасности/infosec-intro/labs/lab5/presentation/image/5773893599266720565.jpg)

## Создание и компиляция программы simpleid.c

![](/home/lupupachileshe/work/study/2024-2025/Основы информационной безопасности/infosec-intro/labs/lab5/presentation/image/5773893599266720580.jpg)


## Запуск simpleid

![](/home/lupupachileshe/work/study/2024-2025/Основы информационной безопасности/infosec-intro/labs/lab5/presentation/image/5773893599266720567.jpg)

## Сравнение с системной программой id

Результаты совпадают: geteuid() и getegid() показывают эффективные идентификаторы, аналогичные id.



## Усложнённая программа simpleid2.c

 #include <sys/types.h>
 #include <unistd.h>
 #include <stdio.h>

int main() {
    uid_t real_uid = getuid();
    uid_t e_uid = geteuid();
    gid_t real_gid = getgid();
    gid_t e_gid = getegid();

    printf("e_uid=%d, e_gid=%d\n", e_uid, e_gid);
    printf("real_uid=%d, real_gid=%d\n", real_uid, real_gid);

    return 0;
}



