---
## Front matter
title: " отчёта по лабораторной работе 5"
subtitle: "Отчёт по лабораторной работе: Идентификаторы пользователя и группы, биты SetUID, SetGID и Sticky"
author: "Чилеше Лупупа "

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

Изучить механизм управления правами доступа в операционной системе Linux на уровне идентификаторов пользователя (UID) и группы (GID), а также на практике освоить работу с бітами SetUID, SetGID и Sticky, их влияние на выполнение программ и управление файлами.
Научиться использовать системные вызовы для получения реальных и эффективных UID/GID, изменять права доступа и анализировать безопасность при совместной работе пользователей.

# Создание программы

1. Вход в систему от имени guest

![](/home/lupupachileshe/work/study/2024-2025/Основы информационной безопасности/infosec-intro/labs/lab5/report/image/5773893599266720565.jpg)

2. Создание и компиляция программы simpleid.c

  #include <sys/types.h>
  #include <unistd.h>
  #include <stdio.h>

int main() {
    uid_t uid = geteuid();
    gid_t gid = getegid();
    printf("uid=%d, gid=%d\n", uid, gid);
    return 0;
}

![](/home/lupupachileshe/work/study/2024-2025/Основы информационной безопасности/infosec-intro/labs/lab5/report/image/5773893599266720580.jpg)

__Компиляция:__ 
gcc simpleid.c -o simpleid

![](/home/lupupachileshe/work/study/2024-2025/Основы информационной безопасности/infosec-intro/labs/lab5/report/image/5773893599266720567.jpg)

4. Запуск simpleid

./simpleid


5. Сравнение с системной программой id
id

6. Усложнённая программа simpleid2.c

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

![](/home/lupupachileshe/work/study/2024-2025/Основы информационной безопасности/infosec-intro/labs/lab5/report/image/5773893599266720568.jpg)

7. Компиляция и запуск:

gcc simpleid2.c -o simpleid2
./simpleid2

![](/home/lupupachileshe/work/study/2024-2025/Основы информационной безопасности/infosec-intro/labs/lab5/report/image/5773893599266720573.jpg)

8. Изменение владельца и установка SetUID:

sudo chown root:guest simpleid2
sudo chmod u+s simpleid2

9. Пояснение:
- chown root:guest — устанавливает владельцем root, а группой — guest.

- chmod u+s — устанавливает SetUID-бит, при этом программа будет выполняться с правами владельца (то есть root).

10. Проверка установки прав:

ls -l simpleid2

![](/home/lupupachileshe/work/study/2024-2025/Основы информационной безопасности/infosec-intro/labs/lab5/report/image/5773893599266720570.jpg)

11. Запуск программы и id:

./simpleid2
id

[](/home/lupupachileshe/work/study/2024-2025/Основы информационной безопасности/infosec-intro/labs/lab5/report/image/5773893599266720572.jpg)

12. Повтор для SetGID:

sudo chown guest:root simpleid2
sudo chmod g+s simpleid2

## Программа readfile.c

13. Код:

  #include <fcntl.h>
  #include <stdio.h>
  #include <sys/stat.h>
  #include <sys/types.h>
  #include <unistd.h>`
int main(int argc, char* argv[]) {
    unsigned char buffer[16];
    size_t bytes_read;
    int i;
    int fd = open(argv[1], O_RDONLY);
    do {
        bytes_read = read(fd, buffer, sizeof(buffer));
        for (i = 0; i < bytes_read; ++i)
            printf("%c", buffer[i]);
    } while (bytes_read == sizeof(buffer));
    close(fd);
    return 0;
}

14. Компиляция:

gcc readfile.c -o readfile

15. Изменение владельца и прав:

sudo chown root:root readfile.c
sudo chmod 600 readfile.c

16. Проверка как guest:

cat readfile.c

17. Установка SetUID для readfile:

sudo chown root:guest readfile
sudo chmod u+s readfile

18. Проверка доступа к readfile.c через программу:

./readfile readfile.c


19. Проверка доступа к /etc/shadow:

./readfile /etc/shadow

__Результат:__ Вывод невозможен — файл защищён дополнительными механизмами безопасности, включая SELinux или AppArmor.

## Исследование Sticky-бита

1. Проверка Sticky-бита у /tmp:

ls -ld /tmp

![](/home/lupupachileshe/work/study/2024-2025/Основы информационной безопасности/infosec-intro/labs/lab5/report/image/5773893599266720569.jpg)

t на конце прав означает установленный Sticky-бит.

2. Создание файла в /tmp:

echo "test" > /tmp/file01.txt

![](/home/lupupachileshe/work/study/2024-2025/Основы информационной безопасности/infosec-intro/labs/lab5/report/image/5773893599266720575.jpg)

3. Изменение прав:

chmod o+rw /tmp/file01.txt
ls -l /tmp/file01.txt

- 4–7. Проверки от имени guest2:

    Чтение файла:  успешно

    Добавление через >>:  успешно

    Перезапись через >:  успешно

8. Проверка содержимого:

cat /tmp/file01.txt

![](/home/lupupachileshe/work/study/2024-2025/Основы информационной безопасности/infosec-intro/labs/lab5/report/image/5773893599266720576.jpg)

9. Удаление файла от guest2:

rm /tmp/file01.txt

Удаление невозможно — включён Sticky-бит.

10. Снятие Sticky-бита:

su -
chmod -t /tmp
exit


- 11–12. Проверка отсутствия Sticky:

ls -ld /tmp

![](/home/lupupachileshe/work/study/2024-2025/Основы информационной безопасности/infosec-intro/labs/lab5/report/image/5773893599266720578.jpg)

Результат: отсутствует t

13. Повтор удаления от guest2: Удаление файла теперь возможно, несмотря на то, что пользователь — не владелец.

14. Наблюдение: Sticky-бит предотвращает удаление файлов пользователями, не являющимися владельцами, даже при наличии прав.

15. Восстановление Sticky:

su -
chmod +t /tmp
exit

![](/home/lupupachileshe/work/study/2024-2025/Основы информационной безопасности/infosec-intro/labs/lab5/report/image/5773893599266720579.jpg)

## Выводы:

- Реальные и эффективные UID/GID показывают, от имени какого пользователя и группы исполняется процесс.

- SetUID позволяет выполнять программу от имени владельца, SetGID — от имени группы, что может быть полезно для доступа к защищённым ресурсам.

- Sticky-бит обеспечивает защиту от удаления чужих файлов в общем каталоге, например, /tmp.

- Использование этих битов должно быть контролируемо, так как они могут быть источником уязвимостей при неправильной настройке.









