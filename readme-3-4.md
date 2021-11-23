# Домашнее задание к занятию "3.4. Операционные системы, лекция 2"

1. На лекции мы познакомились с [node_exporter](https://github.com/prometheus/node_exporter/releases). В демонстрации его исполняемый файл запускался в background. Этого достаточно для демо, но не для настоящей production-системы, где процессы должны находиться под внешним управлением. Используя знания из лекции по systemd, создайте самостоятельно простой [unit-файл](https://www.freedesktop.org/software/systemd/man/systemd.service.html) для node_exporter:
    * поместите его в автозагрузку,
    * предусмотрите возможность добавления опций к запускаемому процессу через внешний файл (посмотрите, например, на `systemctl cat cron`),
    * удостоверьтесь, что с помощью systemctl процесс корректно стартует, завершается, а после перезагрузки автоматически поднимается.
```
Установка node_exporter 
$ wget https://github.com/prometheus/node_exporter/releases/download/v1.3.0/node_exporter-1.3.0.linux-amd64.tar.gz
$ tar zxvf node_exporter-*.linux-amd64.tar.gz
$ cd node_exporter-*.linux-amd64
$ sudo cp node_exporter /usr/local/bin/

Создаем юнит-файл node_exporter.service для системы инициализации systemd:
$ sudo nano /etc/systemd/system/node_exporter.service
------------------------------------
[Unit]
Description=Node Exporter Service

[Service]
ExecStart=/usr/local/bin/node_exporter

[Install]
WantedBy=default.target
------------------------------------
Перечитываем конфигурацию systemd:
$ sudo systemctl daemon-reload
Разрешаем автозапуск:
$ sudo systemctl enable node_exporter
Запускаем службу:
$ sudo systemctl start node_exporter


Для крона (`systemctl cat cron`) задано
ExecStart=/usr/sbin/cron -f $EXTRA_OPTS
systemd будет подгружать переменные окружения при старте cron из файла /etc/default/cron, а параметры запуска искать в переменной EXTRA_OPTS
Мы может задать для сервиса node_exporter аналогичную запись
ExecStart=/usr/local/bin/node_exporter -f $EXTRA_OPTS


Убедимся что служба работает, остановим и снова запустим:
$ ps -e |grep node_exporter   
   1269 ?        00:00:00 node_exporter
$ sudo systemctl stop node_exporter
$ ps -e |grep node_exporter
$ systemctl start node_exporter
$ ps -e |grep node_exporter
   1395man  ?        00:00:00 node_exporter
```

2. Ознакомьтесь с опциями node_exporter и выводом `/metrics` по-умолчанию. Приведите несколько опций, которые вы бы выбрали для базового мониторинга хоста по CPU, памяти, диску и сети.
```
Служба мониторинга выводит метрики через порт 9100 - увидеть их можно вызвав курлом вывод в этот порт:  
$ curl -s localhost:9100/metrics

CPU:
    node_cpu_seconds_total{cpu="0",mode="idle"} 1149.82
    node_cpu_seconds_total{cpu="0",mode="system"} 2.02
    node_cpu_seconds_total{cpu="0",mode="user"} 1.65
    
Memory:
    node_memory_MemAvailable_bytes 1.799405568e+09
    node_memory_MemFree_bytes 1.402843136e+09
    
Disk:
    node_disk_io_time_seconds_total{device="sda"} 
    node_disk_read_bytes_total{device="sda"} 
    node_disk_read_time_seconds_total{device="sda"} 
    node_disk_write_time_seconds_total{device="sda"}
    
Network:
    node_network_receive_errs_total{device="eth0"} 
    node_network_receive_bytes_total{device="eth0"} 
    node_network_transmit_bytes_total{device="eth0"}
    node_network_transmit_errs_total{device="eth0"}
    
```

3. Установите в свою виртуальную машину [Netdata](https://github.com/netdata/netdata). Воспользуйтесь [готовыми пакетами](https://packagecloud.io/netdata/netdata/install) для установки (`sudo apt install -y netdata`). После успешной установки:
    * в конфигурационном файле `/etc/netdata/netdata.conf` в секции [web] замените значение с localhost на `bind to = 0.0.0.0`,
    * добавьте в Vagrantfile проброс порта Netdata на свой локальный компьютер и сделайте `vagrant reload`:
    ```bash
    config.vm.network "forwarded_port", guest: 19999, host: 19999
    ```
    После успешной перезагрузки в браузере *на своем ПК* (не в виртуальной машине) вы должны суметь зайти на `localhost:19999`. Ознакомьтесь с метриками, которые по умолчанию собираются Netdata и с комментариями, которые даны к этим метрикам.
```

```

4. Можно ли по выводу `dmesg` понять, осознает ли ОС, что загружена не на настоящем оборудовании, а на системе виртуализации?
```
dmesg (сокр. от англ. diagnostic message) — команда для вывода буфера сообщений ядра в стандартный поток вывода (stdout) 
Видимо да, так как есть соответсвующие строки: 
$ dmesg | grep virtual

```

5. Как настроен sysctl `fs.nr_open` на системе по-умолчанию? Узнайте, что означает этот параметр. Какой другой существующий лимит не позволит достичь такого числа (`ulimit --help`)?
```
$ /sbin/sysctl -n fs.nr_open
1048576

Это максимальное число открытых дескрипторов для ядра (системы), для пользователя задать больше этого числа нельзя (если не менять). 
Число задается кратное 1024, в данном случае =1024*1024. 

$ ulimit --help

$ ulimit -Sn
1024
мягкий лимит (так же ulimit -n) на пользователя (может быть увеличен в процессе работы)

$ ulimit -Hn
1048576
жесткий лимит на пользователя (не может быть увеличен, только уменьшен)

Оба ulimit -n НЕ могут превысить системный fs.nr_open
```

6. Запустите любой долгоживущий процесс (не `ls`, который отработает мгновенно, а, например, `sleep 1h`) в отдельном неймспейсе процессов; покажите, что ваш процесс работает под PID 1 через `nsenter`. Для простоты работайте в данном задании под root (`sudo -i`). Под обычным пользователем требуются дополнительные опции (`--map-root-user`) и т.д.
```
$ sudo -i
$ lsns
$ screen
$ nnshare -f --pid --mount-proc / bin/bash
$ ps aux
$ nsenter --target 23795 --pid --mount
$ sleep 1h
$ nsenter
$ ps -e | grep sleep
   2020 pts/2    00:00:00 sleep
root@vagrant:~# nsenter --target 2020 --pid --mount
root@vagrant:/# ps
    PID TTY          TIME CMD
      2 pts/0    00:00:00 bash
     11 pts/0    00:00:00 ps
```
 
7. Найдите информацию о том, что такое `:(){ :|:& };:`. Запустите эту команду в своей виртуальной машине Vagrant с Ubuntu 20.04 (**это важно, поведение в других ОС не проверялось**). Некоторое время все будет "плохо", после чего (минуты) – ОС должна стабилизироваться. Вызов `dmesg` расскажет, какой механизм помог автоматической стабилизации. Как настроен этот механизм по-умолчанию, и как изменить число процессов, которое можно создать в сессии?
```
Из предыдущих лекций ясно что это функция внутри "{}", судя по всему с именем ":" , которая после опредения в строке запускает саму себя.
внутринности через поиск нагуглил, пораждает два фоновых процесса самой себя,
получается этакое бинарное дерево плодящее процессы 

А функционал судя по всему этот:
[ 3099.973235] cgroup: fork rejected by pids controller in /user.slice/user-1000.slice/session-4.scope
[ 3103.171819] cgroup: fork rejected by pids controller in /user.slice/user-1000.slice/session-11.scope

Судя по всему, система на основании этих файлов в пользовательской зоне ресурсов имеет определенное ограничение на создаваемые ресурси 
и соответсвенно при превышении начинает блокировать создание числа 

Если установить ulimit -u 50 - число процессов будет ограниченно 50 для пользоователя. 
```
