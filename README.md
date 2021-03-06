# Конспект лекций по курсу операционных систем

*ИТМО, весна 2016*

**Disclaimer:** конспекты не претендуют на академическую ценность, не нравится - не кукарекайте, а ещё лучше, если заметите ошибку, поправьте и сделайте pull request.

Темы лекций:

1. 09.02.16
    * [Теория](lectures/lecture0.md): Текущие абстракции, системные вызовы, простая модель ОС.
    * Практика: На самом деле 2-ая лекция. Практически всё про make и Makefile
2. 16.02.16
    * Теория: Адресация памяти, файловая система (Inode, mount, etc...) *(not implemented yet)*
    * [Практика](practices/practice1/practice1.md): работа с файловыми дескрипторами (open, read, write, close). Статическая и динамическая линковки.
3. 01.03.16
    * [Теория](lectures/lecture2.md): права доступа, владельцы процессов/файлов (uid, gid). Команды sudo, chmod, chown.
    * [Практика](practices/practice2/practice2.md): скрипты на bash, некоторые консольные утилиты.
4. 12.03.16
    * [Теория](lectures/lecture3.md): umask, ссылки, треды, pipe/fifo
    * [Практика](practices/practice3/practice3.md): exec, wait, fork - создание дочерних процессов.
5. 15.03.16
    * [Теория](lectures/lecture4.md): межпроцессное взаимодействие, сигналы. В конце лекции зачем-то 10 минут про линковку.
    * Практика: сдача дз.
6. [22.03.16](lectures/lecture5.md)
    * Многозадачность (сессии, группы процессов, демоны)
7. [29.03.16](lectures/OS-Init.pdf)
    * Ход загрузки системы. Лекцию вел Гриша, он упоротый, поэтому конспект по этой лекции такой же. Зато презенташка есть.
8. [19.04.16](lectures/lecture7.md)
    * Терминалы и псевдотерминалы.
9. [26.04.16](lectures/lecture8.md)
    * Что делать, если много файловых дескрипторов (спойлер: `select/poll/epoll`), сеть.
10. [03.05.16](lectures/lecture9.md)
    * Сеть (продолжение), различные лицензии на ПО.
11. [10.05.16](lectures/lecture10.md)
    * Линковка
12. [17.05.16](lectures/lecture11.md)
    * Как на самом деле делаются системные вызовы, реализация виртуальной памяти, memory overcommit, сырые сокеты.