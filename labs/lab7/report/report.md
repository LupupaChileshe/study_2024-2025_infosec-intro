---
## Front matter
title: "Отчёт по лабораторной работе"
subtitle: "Режим однократного гаммирования"
author: "Дмитрий Сергеевич Кулябов"

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

Изучение принципов шифрования в режиме однократного гаммирования, разработка приложения для шифрования и дешифрования данных, а также анализ криптостойкости метода.

# Задание

Здесь приводится описание задания в соответствии с рекомендациями
методического пособия и выданным вариантом.

# Описание процесса выполнения задания


## Теоретическое введение

Однократное гаммирование — это метод шифрования, при котором каждый символ открытого текста объединяется с соответствующим символом ключа с помощью операции XOR (исключающее ИЛИ).

Формулы:

-    Шифрование: Ci=Pi⊕KiCi​=Pi​⊕Ki​

-    Дешифрование: Pi=Ci⊕KiPi​=Ci​⊕Ki​

-    Нахождение ключа: Ki=Pi⊕CiKi​=Pi​⊕Ci​

## Практическая реализация

Задача 1: Шифрование известного текста с заданным ключом
Шаги:

    Преобразовать текст и ключ в байты (например, в кодировке UTF-8).

    Применить операцию XOR к каждому байту.

    Получить шифротекст.

Задача 2: Подбор ключа для заданного шифротекста

Дано:

    Шифротекст: b'\x12\x45\x67...' (из предыдущего шага)

    Желаемый открытый текст: "С Новым Годом, друзья!"

Шаги:

    Преобразовать желаемый текст в байты.

    Вычислить ключ: Ki=Pi⊕CiKi​=Pi​⊕Ci​.

![](/home/lupupachileshe/work/study/2024-2025/Основы информационной безопасности/infosec-intro/labs/lab7/report/image/Screenshot from 2025-05-17 18-20-29.png)


![](/home/lupupachileshe/work/study/2024-2025/Основы информационной безопасности/infosec-intro/labs/lab7/report/image/Screenshot from 2025-05-17 18-20-38.png)


![](/home/lupupachileshe/work/study/2024-2025/Основы информационной безопасности/infosec-intro/labs/lab7/report/image/Screenshot from 2025-05-17 18-20-50.png)

![](/home/lupupachileshe/work/study/2024-2025/Основы информационной безопасности/infosec-intro/labs/lab7/report/image/Screenshot from 2025-05-17 18-21-32.png)

![](/home/lupupachileshe/work/study/2024-2025/Основы информационной безопасности/infosec-intro/labs/lab7/report/image/Screenshot from 2025-05-17 18-21-46.png)





# Выводы

    Однократное гаммирование обеспечивает высокую криптостойкость при соблюдении условий (ключ одноразовый, длина ключа равна длине текста).

    Метод уязвим при повторном использовании ключа или если ключ короче текста.

    Операция XOR обратима, что делает метод удобным для шифрования и дешифрования.
    
    
# Ответы на контрольные вопросы 

1. Смысл однократного гаммирования
    Это метод шифрования, где каждый бит открытого текста комбинируется с битом ключа через XOR.
2. Недостатки

        Требуется ключ той же длины, что и сообщение.

        Ключ нельзя использовать повторно.

3. Преимущества

        Абсолютная стойкость при правильном использовании.

        Простота реализации.

4. Почему длина ключа должна совпадать с длиной текста?
    Если ключ короче, его приходится повторять, что снижает стойкость.

5. Используемая операция
    XOR — обратимая битовая операция.

6. Как получить шифротекст?
    Ci=Pi⊕KiCi​=Pi​⊕Ki​.

7. Как получить ключ?
    Ki=Pi⊕CiKi​=Pi​⊕Ci​.

8. Условия абсолютной стойкости

        Ключ должен быть случайным.

        Длина ключа ≥ длины текста.

        Ключ используется однократно.  
    
    
    
    
    
    
    
    
    
    
    
    
    
    
    
    
    
