## Домашнее задание к занятию «3.3. Операционные системы, лекция 1»

1. Какой системный вызов делает команда cd? В прошлом ДЗ мы выяснили, что cd не является самостоятельной программой, это shell builtin, поэтому запустить strace непосредственно на cd не получится. Тем не менее, вы можете запустить strace на /bin/bash -c 'cd /tmp'. В этом случае вы увидите полный список системных вызовов, которые делает сам bash при старте. Вам нужно найти тот единственный, который относится именно к cd. Обратите внимание, что strace выдаёт результат своей работы в поток stderr, а не в stdout.
```
$ strace /bin/bash -c 'cd /tmp'
Системный вызов: chdir("/tmp")
```

2. Попробуйте использовать команду file на объекты разных типов на файловой системе. Например:
```
vagrant@netology1:~$ file /dev/tty
/dev/tty: character special (5/0)
vagrant@netology1:~$ file /dev/sda
/dev/sda: block special (8/0)
vagrant@netology1:~$ file /bin/bash
/bin/bash: ELF 64-bit LSB shared object, x86-64
```
Используя strace выясните, где находится база данных file на основании которой она делает свои догадки.
```
команда file определяет тип файлов
$ strace file /dev/tty
openat(AT_FDCWD, "/usr/share/misc/magic.mgc", O_RDONLY) = 3
База данных типов находится в: /usr/share/misc/magic.mgc
```

3. Предположим, приложение пишет лог в текстовый файл. Этот файл оказался удален (deleted в lsof), однако возможности сигналом сказать приложению переоткрыть файлы или просто перезапустить приложение – нет. Так как приложение продолжает писать в удаленный файл, место на диске постепенно заканчивается. Основываясь на знаниях о перенаправлении потоков предложите способ обнуления открытого удаленного файла (чтобы освободить место на файловой системе).
```
$ ping 192.168.0.1 > log &
[2] 1307
$ sudo lsof -p 1307 | grep log
ping    1307 vagrant    1w   REG  253,0     6305 131092 /home/vagrant/log
$ rm log
$ sudo lsof -p 1307 | grep log
ping    1307 vagrant    1w   REG  253,0    14465 131092 /home/vagrant/log (deleted)
Обнуление через перенаправление:
$ sudo echo '' > /proc/1307/fd/1 > /dev/null
где 1307 - PID процесса ping
1 - дескриптор файла , который удалил
НО получаю ошибку  Permission denied
Что я делаю не так?
```

4.Занимают ли зомби-процессы какие-то ресурсы в ОС (CPU, RAM, IO)?
```
Зомби-процессы освобождают свои ресурсы, но не освобождают запись в таблице процессов. 
```

5.В iovisor BCC есть утилита opensnoop:
root@vagrant:~# dpkg -L bpfcc-tools | grep sbin/opensnoop
/usr/sbin/opensnoop-bpfcc
На какие файлы вы увидели вызовы группы open за первую секунду работы утилиты? Воспользуйтесь пакетом bpfcc-tools для Ubuntu 20.04. Дополнительные сведения по установке https://github.com/iovisor/bcc/blob/master/INSTALL.md#ubuntu---source
```
$ sudo apt-get install bpfcc-tools linux-headers-$(uname -r)
$ dpkg -L bpfcc-tools | grep sbin/opensnoop
/usr/sbin/opensnoop-bpfcc
$ sudo /usr/sbin/opensnoop-bpfcc
PID    COMM               FD ERR PATH
810    vminfo              6   0 /var/run/utmp
601    dbus-daemon        -1   2 /usr/local/share/dbus-1/system-services
601    dbus-daemon        18   0 /usr/share/dbus-1/system-services
601    dbus-daemon        -1   2 /lib/dbus-1/system-services
601    dbus-daemon        18   0 /var/lib/snapd/dbus-1/system
```

6.Какой системный вызов использует uname -a? Приведите цитату из man по этому системному вызову, где описывается альтернативное местоположение в /proc, где можно узнать версию ядра и релиз ОС.
```
Системный вызов: uname()
uname({sysname="Linux", nodename="vagrant", ...}) = 0
Цитата:
Part of the utsname information is also accessible  via  /proc/sys/ker‐
       nel/{ostype, hostname, osrelease, version, domainname}.
```

7. Чем отличается последовательность команд через ; и через && в bash? Например:
```
root@netology1:~# test -d /tmp/some_dir; echo Hi
Hi
root@netology1:~# test -d /tmp/some_dir && echo Hi
root@netology1:~#
```
Есть ли смысл использовать в bash &&, если применить set -e?
```
&& -  условный оператор  
;  - разделитель последовательных команд

test -d /tmp/some_dir && echo Hi - echo отработает (выведет "Hi") только в случае успешного завершения команды test

set -e - прерывает сессию при любом ненулевом значении исполняемых команд в конвеере кроме последней.
в случае &&  вместе с set -e- вероятно не имеет смысла, так как при ошибке выполнение команд прекратиться. 
```

8. Из каких опций состоит режим bash set -euxo pipefail и почему его хорошо было бы использовать в сценариях?
```
-e прерывает выполнение исполнения при ошибке любой команды кроме последней в последовательности 
-x вывод трейса простых команд 
-u неустановленные/не заданные параметры и переменные считаются как ошибки, с выводом в stderr текста ошибки и выполнит завершение неинтерактивного вызова
-o pipefail возвращает код возврата набора/последовательности команд, ненулевой при последней команды или 0 для успешного выполнения команд.
```

9. Используя -o stat для ps, определите, какой наиболее часто встречающийся статус у процессов в системе. В man ps ознакомьтесь (/PROCESS STATE CODES) что значат дополнительные к основной заглавной буквы статуса процессов. Его можно не учитывать при расчете (считать S, Ss или Ssl равнозначными).
```
$ ps -o stat
STAT
Ss
T
S
R+

Самые частые S* (S,Ss) - процессы ожидающие завершения (спящие с прерыванием "сна") 
 S    interruptible sleep (waiting for an event to complete)
доп символы это доп характеристики, например приоритет.
 s    is a session leader
 +    is in the foreground process group
```
  
