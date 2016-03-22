#Многозадачность

*Группа процессов* - то, что запустил shell одной командой. Т. е. запущенный `cat` группа процессов,
и `cat t.txt | grep smth | xargs ...` тоже одна группа процессов.
Когда мы хотим завершить работу команды, очевидно, мы хотим послать `SIGINT` всем сразу. Именно это группы процессов и позволяют.

Узнать, какой группе принадлежит процесс, можно с помощью `getpgid()`. Он возвращает номер лидера группы.

*Лидер группы* - процесс, по которому определяется группа.

`kill -pid SIGINT` (в смысле с отрицательным pid) - убивает всю группу с лидером `pid`.

*Сессия* (нет, не та, на которой вы это будете читать) - мн-во групп процессов. У неё тоже есть лидер. 
Типичный лидер сессии - `bash`. К каждой сессии прикреплено не более одного терминала.

*foreground group* - та группа, которой дали (читать с терминала/выводить/etc). *background* - соответственно, остальные.
За их переключение отвечает лидер сессии. Переключение происходит, когда все процессы в группе остановлены. (например, по `Crtl+Z` = `SIGTSTP`).
Как об этом узнает лидер? С помощью `waitpid` (он может отслеживать не только когда процессы умирают, но и просто когда меняют состояние).

`SIGTTIN` - то что прилетает background группам. Но всякие `read` рестартуются. Это поведение зависит от каких-то упоротых флажков 
в sighandler-ах.

`setpgid(pid, pgid)` - перкидывает процесс `pid` в группу `pgid`. Можно менять, если:
`pid` - ребёнок того, кто делает set и он не делал `exec`. Проблема: 
```c
pid = fork()
if (pid == 0) {
    ...
    exec(...)
} else {
    ...
    kill(-pid, SIGUSR1); // что-то послали
}
```
Мы не знаем, кто будет выполняться раньше: ребёнок или родитель. Но нам где-то нужно поменять группу, чтобы слать сигналы, и сделать это до `exec`.
Ноооо..... ДАВАЙТЕ СДЕЛАЕМ ЕГО ДВАЖДЫ!!!
```c
pid = fork()
if (pid == 0) {
    setpgid(pid, ...);
    ...
    exec(...)
} else {
    setpgid(pid, ...);
    ...
    kill(-pid, SIGUSR1); // что-то послали
}
```
Чтобы сделать себя лидером сессии, есть `setsid`. На самом деле, процесс, вызвавший это, создает себе отдельную сессию и становится её лидером (и лидером новой группы в этой сессии).
*NB: нельзя делать `setsid` тем, кто является лидером группы. Потому что `pid` сохраняется, и у нас получится два процесса вроде как одной группы, но в разных сессиях.*

#ДЕМОНЫ

Мы хотим процесс, который не был бы привязан ни к какому терминалу. Надо бы создать новую сессию.
Напрямую вызвать от демона `setsid` мы не можем, т.к. он лидер группы. Давайте заведем процесс, который будет запускать демонов - `run_daemon`. 
Но теперь наш новый процесс - лидер сессии, и может управлять терминалами, а мы не хотим этого. Давайте породим ЕЩЁ один процесс, который уже не будет лидером сессии, а его ребёнком.
Вот уже он может являться демоном. Что делать с 2мя лишними процессами? ~~ПРОСТО УБЕЙ ИХ.~~ Сессия и группа могут нормально жить без лидера.
 
##Терминалы
Когда терминал отключается, он посылает `SIGHUP`. `bash` обычно пересылает этот сигнал дальше. 
Действие по умолчанию - завершить процесс. Демонам он придти просто так не может, поэтому есть соглашение, что по `SIGHUP` он перечитывает конфиг.