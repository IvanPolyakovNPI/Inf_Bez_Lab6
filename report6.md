---
# Front matter
title: "Лаб.6 Мандатное разграничение прав в Linux"
author: "Поляков Иван Андреевич"

# Generic otions
lang: ru-RU
toc-title: "Содержание"

# Bibliography
bibliography: bib/cite.bib
csl: pandoc/csl/gost-r-7-0-5-2008-numeric.csl

# Pdf output format
toc: true # Table of contents
toc_depth: 2
lof: true # List of figures
lot: true # List of tables
fontsize: 12pt
linestretch: 1.5
papersize: a4
documentclass: scrreprt
## I18n
polyglossia-lang:
  name: russian
  options:
	- spelling=modern
	- babelshorthands=true
polyglossia-otherlangs:
  name: english
### Fonts
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
## Misc options
indent: true
header-includes:
  - \linepenalty=10 # the penalty added to the badness of each line within a paragraph (no associated penalty node) Increasing the value makes tex try to have fewer lines in the paragraph.
  - \interlinepenalty=0 # value of the penalty (node) added after each line of a paragraph.
  - \hyphenpenalty=50 # the penalty for line breaking at an automatically inserted hyphen
  - \exhyphenpenalty=50 # the penalty for line breaking at an explicit hyphen
  - \binoppenalty=700 # the penalty for breaking a line at a binary operator
  - \relpenalty=500 # the penalty for breaking a line at a relation
  - \clubpenalty=150 # extra penalty for breaking after first line of a paragraph
  - \widowpenalty=150 # extra penalty for breaking before last line of a paragraph
  - \displaywidowpenalty=50 # extra penalty for breaking before last line before a display math
  - \brokenpenalty=100 # extra penalty for page breaking after a hyphenated line
  - \predisplaypenalty=10000 # penalty for breaking before a display
  - \postdisplaypenalty=0 # penalty for breaking after a display
  - \floatingpenalty = 20000 # penalty for splitting an insertion (can only be split footnote in standard LaTeX)
  - \raggedbottom # or \flushbottom
  - \usepackage{float} # keep figures where there are in the text
  - \floatplacement{figure}{H} # keep figures where there are in the text
  - \usepackage[T2A]{fontenc}
---

# Цель работы

Развить навыки администрирования ОС Linux. Получить первое практическое знакомство с технологией SELinux1.
Проверить работу SELinx на практике совместно с веб-сервером Apache.

# Последовательность выполнения работы
## Создание программы
1. Войдите в систему с полученными учётными данными и убедитесь, что
SELinux работает в режиме enforcing политики targeted с помощью команд getenforce и sestatus.

![Рис. 1](img6/1.png){ #fig:001 width=100% }

2. Обратитесь с помощью браузера к веб-серверу, запущенному на вашем
компьютере, и убедитесь, что последний работает:

![Рис. 2](img6/2.png){ #fig:002 width=100% }

3. Найдите веб-сервер Apache в списке процессов, определите его контекст
безопасности и занесите эту информацию в отчёт. Например, можно использовать команду

![Рис. 3](img6/3.png){ #fig:003 width=100% }

4. Посмотрите текущее состояние переключателей SELinux для Apache с
помощью команды

![Рис. 4](img6/4.png){ #fig:004 width=100% }

5. Посмотрите статистику по политике с помощью команды seinfo, также
определите множество пользователей, ролей, типов.

![Рис. 5](img6/5.png){ #fig:005 width=100% 

6. Определите тип файлов и поддиректорий, находящихся в директории
/var/www, с помощью команды

![Рис. 6](img6/6.png){ #fig:006 width=100% }

7. Определите тип файлов, находящихся в директории /var/www/html:

![Рис. 7](img6/7.png){ #fig:007 width=100% }

8. Определите круг пользователей, которым разрешено создание файлов в
директории /var/www/html.

![Рис. 8](img6/8.png){ #fig:008 width=100% }

9. Создайте от имени суперпользователя (так как в дистрибутиве после установки только ему разрешена запись в директорию) html-файл
/var/www/html/test.html следующего содержания:

![Рис. 9](img6/9.png){ #fig:009 width=100% }

10. Проверьте контекст созданного вами файла. Занесите в отчёт контекст,
присваиваемый по умолчанию вновь созданным файлам в директории
/var/www/html

![Рис. 10](img6/10.png){ #fig:010 width=100% }

11. Обратитесь к файлу через веб-сервер, введя в браузере адрес
http://127.0.0.1/test.html. Убедитесь, что файл был успешно отображён.

![Рис. 11](img6/11.png){ #fig:011 width=100% }

12. Изучите справку man httpd_selinux и выясните, какие контексты файлов определены для httpd. Сопоставьте их с типом файла
test.html. Проверить контекст файла можно командой ls -Z.

![Рис. 12](img6/12.png){ #fig:012 width=100% }

13. Измените контекст файла /var/www/html/test.html с
httpd_sys_content_t на любой другой, к которому процесс httpd не
должен иметь доступа, например, на samba_share_t:

![Рис. 13](img6/13.png){ #fig:013 width=100% }

14. Попробуйте ещё раз получить доступ к файлу через веб-сервер, введя в
браузере адрес http://127.0.0.1/test.html. Вы должны получить
сообщение об ошибке:

![Рис. 14](img6/14.png){ #fig:014 width=100% }

15. Проанализируйте ситуацию. Почему файл не был отображён, если права
доступа позволяют читать этот файл любому пользователю?

![Рис. 15](img6/15.png){ #fig:015 width=100% }

16. Попробуйте запустить веб-сервер Apache на прослушивание ТСР-порта 81 (а не 80, как рекомендует IANA и прописано в /etc/services). Для
этого в файле /etc/httpd/httpd.conf найдите строчку Listen 80 и
замените её на Listen 81.

![Рис. 16](img6/16.png){ #fig:016 width=100% }

17. Выполните перезапуск веб-сервера Apache. Произошёл сбой? Поясните
почему?

![Рис. 17](img6/17.png){ #fig:017 width=100% }

18. Проанализируйте лог-файлы:

![Рис. 18](img6/18.png){ #fig:018 width=100% }

19. Выполните команду

![Рис. 19](img6/19.png){ #fig:019 width=100% }

20. Попробуйте запустить веб-сервер Apache ещё раз. Поняли ли вы, почему
он сейчас запустился, а в предыдущем случае не смог?

![Рис. 20](img6/20.png){ #fig:020 width=100% }

21. Верните контекст httpd_sys_cоntent__t к файлу /var/www/html/ test.html:
chcon -t httpd_sys_content_t /var/www/html/test.html
После этого попробуйте получить доступ к файлу через веб-сервер, введя в браузере адрес http://127.0.0.1:81/test.html.

![Рис. 21](img6/21.png){ #fig:021 width=100% }

22. Исправьте обратно конфигурационный файл apache, вернув Listen 80.

![Рис. 22](img6/22.png){ #fig:022 width=100% }

23. Удалите привязку http_port_t к 81 порту:

![Рис. 23](img6/23.png){ #fig:023 width=100% }

24. Удалите файл /var/www/html/test.html:

![Рис. 24](img6/24.png){ #fig:024 width=100% }

# Выводы

Развил навыки администрирования ОС Linux. Получил первое практическое знакомство с технологией SELinux1.
Проверил работу SELinx на практике совместно с веб-сервером Apache.