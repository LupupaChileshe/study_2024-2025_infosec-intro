---
## Front matter
title: "Работа с SELinux и веб-сервером Apache"
subtitle: "LAB 6"
author: "Чилеше Лупупа"

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
mainfont: PT Serif
romanfont: PT Serif
sansfont: PT Sans
monofont: PT Mono
mainfontoptions: Ligatures=TeX
romanfontoptions: Ligatures=TeX
sansfontoptions: Ligatures=TeX,Scale=MatchLowercase
monofontoptions: Scale=MatchLowercase,Scale=0.9
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

Освоить практическую работу с механизмами безопасности SELinux в режиме политики enforcing, изучить работу веб-сервера Apache в условиях SELinux, провести манипуляции с контекстами безопасности и портами, определить влияние SELinux на доступность файлов и служб.

## 1. Проверка режима работы SELinux

getenforce
sestatus

__Описание:__ Убедились, что SELinux работает в режиме enforcing с политикой targeted.

![](/home/lupupachileshe/work/study/2024-2025/Основы информационной безопасности/infosec-intro/labs/lab6/report/image/Screenshot from 2025-05-03 13-42-17.png)

## 2. Проверка статуса веб-сервера Apache

service httpd status
   или
/etc/rc.d/init.d/httpd status

![](/home/lupupachileshe/work/study/2024-2025/Основы информационной безопасности/infosec-intro/labs/lab6/report/image/Screenshot from 2025-05-03 13-52-52.png)

## 3. Поиск процесса Apache и его контекста SELinux

ps auxZ | grep httpd

__Результат:__ Контекст процесса: system_u:system_r:httpd_t:s0

![](/home/lupupachileshe/work/study/2024-2025/Основы информационной безопасности/infosec-intro/labs/lab6/report/image/Screenshot from 2025-05-03 13-53-13.png)


## 4. Просмотр переключателей SELinux для Apache

sestatus -b | grep httpd

![]()

## 5. Информация о политике SELinux

seinfo

![](/home/lupupachileshe/work/study/2024-2025/Основы информационной безопасности/infosec-intro/labs/lab6/report/image/Screenshot from 2025-05-03 13-58-26.png)

__Результат:__ Получены сведения о пользователях, ролях, типах, например:

- Пользователи: unconfined_u, system_u

- Роли: object_r, system_r

- Типы: httpd_t, httpd_sys_content_t

## 6–7. Типы файлов в /var/www и /var/www/html

ls -lZ /var/www
ls -lZ /var/www/html

![](/home/lupupachileshe/work/study/2024-2025/Основы информационной безопасности/infosec-intro/labs/lab6/report/image/Screenshot from 2025-05-03 13-59-35.png)

![](/home/lupupachileshe/work/study/2024-2025/Основы информационной безопасности/infosec-intro/labs/lab6/report/image/Screenshot from 2025-05-03 14-00-02.png)

## 8. Проверка прав пользователей на создание файлов

Вывод команды ls -ldZ /var/www/html

![](/home/lupupachileshe/work/study/2024-2025/Основы информационной безопасности/infosec-intro/labs/lab6/report/image/Screenshot from 2025-05-03 14-01-21.png)

Анализ: Только root имеет права записи. Пользователи без расширенных политик не могут записывать файлы без смены контекста.


## 9. Создание HTML-файла

echo "<html><body>test</body></html>" > /var/www/html/test.html

![](/home/lupupachileshe/work/study/2024-2025/Основы информационной безопасности/infosec-intro/labs/lab6/report/image/Screenshot from 2025-05-03 14-06-20.png)

## 10. Проверка контекста файла

ls -Z /var/www/html/test.html

Результат: unconfined_u:object_r:httpd_sys_content_t:s0

![](/home/lupupachileshe/work/study/2024-2025/Основы информационной безопасности/infosec-intro/labs/lab6/report/image/Screenshot from 2025-05-03 14-06-41.png)

## 11. Проверка доступа к файлу через браузер

URL: http://127.0.0.1/test.html
Результат: Файл успешно отображён.

## 12. Анализ справки man httpd_selinux

man httpd_selinux

__Результат:__ Тип httpd_sys_content_t разрешает доступ для httpd. Контекст файла соответствует этому типу, поэтому доступ был разрешён.

## 13. Смена типа контекста на samba_share_t

chcon -t samba_share_t /var/www/html/test.html
ls -Z /var/www/html/test.html
![](/home/lupupachileshe/work/study/2024-2025/Основы информационной безопасности/infosec-intro/labs/lab6/report/image/Screenshot from 2025-05-03 14-19-50.png)

## 14. Попытка открыть файл в браузере

Результат:
Ошибка: 403 Forbidden
##15. Анализ проблемы

__Причина:__ SELinux запрещает httpd доступ к файлам с типом samba_share_t.

__Команды:__

ls -l /var/www/html/test.html
tail /var/log/messages
tail /var/log/audit/audit.log

__Вывод:__ Запись в audit.log подтверждает блокировку доступа.

## 16. Изменение порта Apache

Файл конфигурации:

/etc/httpd/conf/httpd.conf

Изменение: Listen 80 → Listen 81
## 17. Перезапуск Apache

service httpd restart

![](/home/lupupachileshe/work/study/2024-2025/Основы информационной безопасности/infosec-intro/labs/lab6/report/image/Screenshot from 2025-05-03 14-20-33.png)

Результат: Ошибка запуска — SELinux блокирует прослушивание несогласованного порта.
## 18. Анализ логов

tail -n 50 /var/log/messages
tail -n 50 /var/log/httpd/error_log
tail -n 50 /var/log/audit/audit.log

## 19. Разрешение порта 81

semanage port -a -t http_port_t -p tcp 81
semanage port -l | grep http_port_t

__Результат:__ Порт 81 добавлен в список разрешённых.

## 20. Перезапуск Apache снова

service httpd restart

__Результат:__ Успешный запуск, т.к. SELinux теперь разрешает httpd слушать порт 81.
## 21. Возврат контекста файла

chcon -t httpd_sys_content_t /var/www/html/test.html

Проверка:
URL: http://127.0.0.1:81/test.html
Результат: Файл успешно отображён.
22–23. Возврат конфигурации и удаление порта

 # В httpd.conf
Listen 80

semanage port -d -t http_port_t -p tcp 81
semanage port -l | grep http_port_t

## 24. Удаление файла

rm /var/www/html/test.html

# Выводы

В ходе лабораторной работы был изучен механизм SELinux и его взаимодействие с веб-сервером Apache. Были выполнены практические действия по изменению контекстов безопасности, настройке доступа к файлам и смене портов. Установлено, что SELinux эффективно ограничивает доступ к ресурсам, даже при наличии стандартных прав доступа, и требует явной настройки безопасности для каждого компонента. Работа позволила глубже понять внутренние механизмы безопасности Linux.


