## Домашнее задание к занятию "3.7. Компьютерные сети, лекция 2"

1. Проверьте список доступных сетевых интерфейсов на вашем компьютере. Какие команды есть для этого в Linux и в Windows?
```
Команда в Linux:
$ ip -c -br link
lo               UNKNOWN        00:00:00:00:00:00 <LOOPBACK,UP,LOWER_UP>
eth0             UP             08:00:27:73:60:cf <BROADCAST,MULTICAST,UP,LOWER_UP>
# ip address или $ ip a
более сокращенный вариант - опрос состояния интерфейсов $ ip link
$ ls /sys/class/net (Ядро Linux сохраняет данные о сетевом интерфейсе в каталоге /sys/class/net)
br-f055685b73ee  docker0  enp0s3  lo  veth3b77207  veth7d718e7  veth8a8feef  vetha6e9f92

$ ip a
1: lo: <LOOPBACK,UP,LOWER_UP> mtu 65536 qdisc noqueue state UNKNOWN group default qlen 1000
    link/loopback 00:00:00:00:00:00 brd 00:00:00:00:00:00
    inet 127.0.0.1/8 scope host lo
       valid_lft forever preferred_lft forever
    inet6 ::1/128 scope host 
       valid_lft forever preferred_lft forever
2: enp0s3: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc fq_codel state UP group default qlen 1000
    link/ether 08:00:27:5e:d9:19 brd ff:ff:ff:ff:ff:ff
    inet 192.168.0.104/24 brd 192.168.0.255 scope global dynamic noprefixroute enp0s3
       valid_lft 16sec preferred_lft 16sec
    inet6 fe80::1a02:a659:5061:9522/64 scope link noprefixroute 
       valid_lft forever preferred_lft forever
3: docker0: <NO-CARRIER,BROADCAST,MULTICAST,UP> mtu 1500 qdisc noqueue state DOWN group default qlen 1000
    link/ether 00:00:00:00:00:00 brd ff:ff:ff:ff:ff:ff
    inet 172.17.0.1/16 brd 172.17.255.255 scope global noprefixroute docker0
       valid_lft forever preferred_lft forever
4: br-f055685b73ee: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc noqueue state UP group default qlen 1000
    link/ether 36:b1:8a:ad:86:08 brd ff:ff:ff:ff:ff:ff
    inet 172.18.0.1/16 brd 172.18.255.255 scope global noprefixroute br-f055685b73ee
       valid_lft forever preferred_lft forever
6: veth3b77207@if5: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc noqueue master br-f055685b73ee state UP group default 
    link/ether 8a:ba:57:02:77:70 brd ff:ff:ff:ff:ff:ff link-netnsid 0
    inet6 fe80::88ba:57ff:fe02:7770/64 scope link 
       valid_lft forever preferred_lft forever
8: vetha6e9f92@if7: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc noqueue master br-f055685b73ee state UP group default 
    link/ether 46:e8:9a:0b:79:79 brd ff:ff:ff:ff:ff:ff link-netnsid 1
    inet6 fe80::44e8:9aff:fe0b:7979/64 scope link 
       valid_lft forever preferred_lft forever
12: veth7d718e7@if11: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc noqueue master br-f055685b73ee state UP group default 
    link/ether 6a:1a:d1:78:c4:6a brd ff:ff:ff:ff:ff:ff link-netnsid 3
    inet6 fe80::681a:d1ff:fe78:c46a/64 scope link 
       valid_lft forever preferred_lft forever
14: veth8a8feef@if13: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc noqueue master br-f055685b73ee state UP group default 
    link/ether 36:b1:8a:ad:86:08 brd ff:ff:ff:ff:ff:ff link-netnsid 4
    inet6 fe80::34b1:8aff:fead:8608/64 scope link 
       valid_lft forever preferred_lft forever

Команда в Windows: ipconfig  
C:\WINDOWS\system32>ipconfig -all

Настройка протокола IP для Windows

   Имя компьютера  . . . . . . . . . : DESKTOP-SR7H4HA
   Основной DNS-суффикс  . . . . . . :
   Тип узла. . . . . . . . . . . . . : Гибридный
   IP-маршрутизация включена . . . . : Нет
   WINS-прокси включен . . . . . . . : Нет

Адаптер Ethernet Ethernet:

   Состояние среды. . . . . . . . : Среда передачи недоступна.
   DNS-суффикс подключения . . . . . :
   Описание. . . . . . . . . . . . . : Realtek PCIe GBE Family Controller
   Физический адрес. . . . . . . . . : 2C-FD-A1-6D-FD-6E
   DHCP включен. . . . . . . . . . . : Да
   Автонастройка включена. . . . . . : Да

Адаптер Ethernet Ethernet 2:

   DNS-суффикс подключения . . . . . :
   Описание. . . . . . . . . . . . . : VirtualBox Host-Only Ethernet Adapter #2
   Физический адрес. . . . . . . . . : 0A-00-27-00-00-0E
   DHCP включен. . . . . . . . . . . : Да
   Автонастройка включена. . . . . . : Да
   Локальный IPv6-адрес канала . . . : fe80::3859:10d5:f18b:6d43%14(Основной)
   Автонастройка IPv4-адреса . . . . : 169.254.109.67(Основной)
   Маска подсети . . . . . . . . . . : 255.255.0.0
   Основной шлюз. . . . . . . . . :
   IAID DHCPv6 . . . . . . . . . . . : 352845863
   DUID клиента DHCPv6 . . . . . . . : 00-01-00-01-21-F7-6D-E8-2C-FD-A1-6D-FD-6E
   DNS-серверы. . . . . . . . . . . : fec0:0:0:ffff::1%1
                                       fec0:0:0:ffff::2%1
                                       fec0:0:0:ffff::3%1
   NetBios через TCP/IP. . . . . . . . : Включен

Адаптер беспроводной локальной сети Беспроводная сеть:

   DNS-суффикс подключения . . . . . :
   Описание. . . . . . . . . . . . . : TP-LINK 150Mbps Wireless Lite N Adapter
   Физический адрес. . . . . . . . . : 74-EA-3A-90-D6-65
   DHCP включен. . . . . . . . . . . : Да
   Автонастройка включена. . . . . . : Да
   Локальный IPv6-адрес канала . . . : fe80::94e8:5eca:8246:cd1c%6(Основной)
   IPv4-адрес. . . . . . . . . . . . : 192.168.0.102(Основной)
   Маска подсети . . . . . . . . . . : 255.255.255.0
   Аренда получена. . . . . . . . . . : 2 декабря 2021 г. 11:25:50
   Срок аренды истекает. . . . . . . . . . : 2 декабря 2021 г. 13:54:09
   Основной шлюз. . . . . . . . . : 192.168.0.1
   DHCP-сервер. . . . . . . . . . . : 192.168.0.1
   IAID DHCPv6 . . . . . . . . . . . : 359983674
   DUID клиента DHCPv6 . . . . . . . : 00-01-00-01-21-F7-6D-E8-2C-FD-A1-6D-FD-6E
   DNS-серверы. . . . . . . . . . . : 192.168.0.1
   NetBios через TCP/IP. . . . . . . . : Включен

Адаптер Ethernet vEthernet (Default Switch):

   DNS-суффикс подключения . . . . . :
   Описание. . . . . . . . . . . . . : Hyper-V Virtual Ethernet Adapter
   Физический адрес. . . . . . . . . : 00-15-5D-FD-E5-70
   DHCP включен. . . . . . . . . . . : Нет
   Автонастройка включена. . . . . . : Да
   Локальный IPv6-адрес канала . . . : fe80::d4fa:e895:4296:d1ca%24(Основной)
   IPv4-адрес. . . . . . . . . . . . : 172.17.47.81(Основной)
   Маска подсети . . . . . . . . . . : 255.255.255.240
   Основной шлюз. . . . . . . . . :
   IAID DHCPv6 . . . . . . . . . . . : 402658653
   DUID клиента DHCPv6 . . . . . . . : 00-01-00-01-21-F7-6D-E8-2C-FD-A1-6D-FD-6E
   DNS-серверы. . . . . . . . . . . : fec0:0:0:ffff::1%1
                                       fec0:0:0:ffff::2%1
                                       fec0:0:0:ffff::3%1
   NetBios через TCP/IP. . . . . . . . : Включен
   
Можно посмотреть и там и там ARP-таблицу
>arp -a

Интерфейс: 192.168.0.102 --- 0x6
  адрес в Интернете      Физический адрес      Тип
  192.168.0.1           30-b5-c2-69-ca-f2     динамический
  192.168.0.255         ff-ff-ff-ff-ff-ff     статический
  224.0.0.2             01-00-5e-00-00-02     статический
  224.0.0.22            01-00-5e-00-00-16     статический
  224.0.0.251           01-00-5e-00-00-fb     статический
  224.0.0.252           01-00-5e-00-00-fc     статический
  239.255.255.250       01-00-5e-7f-ff-fa     статический
  255.255.255.255       ff-ff-ff-ff-ff-ff     статический

Интерфейс: 169.254.109.67 --- 0xe
  адрес в Интернете      Физический адрес      Тип
  169.254.255.255       ff-ff-ff-ff-ff-ff     статический
  224.0.0.2             01-00-5e-00-00-02     статический
  224.0.0.22            01-00-5e-00-00-16     статический
  224.0.0.251           01-00-5e-00-00-fb     статический
  224.0.0.252           01-00-5e-00-00-fc     статический
  239.255.255.250       01-00-5e-7f-ff-fa     статический
  255.255.255.255       ff-ff-ff-ff-ff-ff     статический

Интерфейс: 172.17.47.81 --- 0x18
  адрес в Интернете      Физический адрес      Тип
  172.17.47.95          ff-ff-ff-ff-ff-ff     статический
  224.0.0.2             01-00-5e-00-00-02     статический
  224.0.0.22            01-00-5e-00-00-16     статический
  224.0.0.251           01-00-5e-00-00-fb     статический
  239.255.255.250       01-00-5e-7f-ff-fa     статический
  255.255.255.255       ff-ff-ff-ff-ff-ff     статический
```

2. Какой протокол используется для распознавания соседа по сетевому интерфейсу? Какой пакет и команды есть в Linux для этого?
```
Link Discovery дает возможность получать информацию о линках через SNMP,
необходимо установить два пакета snmpd и lldpd
sudo apt install lldpd snmpd
команда: lldpctl
```

3. Какая технология используется для разделения L2 коммутатора на несколько виртуальных сетей? Какой пакет и команды есть в Linux для этого? Приведите пример конфига.
```
VLAN (Virtual Local Area Network) — группа устройств, имеющих возможность взаимодействовать между собой напрямую на канальном уровне, хотя физически при этом они могут быть подключены к разным сетевым коммутаторам. И наоборот, устройства, находящиеся в разных VLAN'ах, невидимы друг для друга на канальном уровне, даже если они подключены к одному коммутатору, и связь между этими устройствами возможна только на сетевом и более высоких уровнях.
В современных сетях VLAN — главный механизм для создания логической топологии сети, не зависящей от её физической топологии. VLAN'ы используются для сокращения широковещательного трафика в сети. Имеют большое значение с точки зрения безопасности, в частности как средство борьбы с ARP-spoofing'ом.

Используется пакет vlan: $ sudo apt install vlan
Команды: vlan, interface

Произведем настройку vlan на сетевом интерфейсе комутатора:
#conf t
Создаем виртуальную сеть
#vlan 2
Даем имя
#name test-vlan
#exit
Привязываем интерфейсы
#interface range FatEthernet 0/13 - 14
#switchport mode access
#switchport access vlan 2
Ставим голос на 3й влан:
#switchport voice vlan 3
#exit
(источник http://snakeproject.ru/rubric/article.php?art=cisco_vlan)

Задание для Linux: vlan нужно настроить на сетевом интерфейсе хоста, не коммутатора
Источники
http://xgu.ru/wiki/VLAN_%D0%B2_Linux
https://itproffi.ru/nastrojka-vlan-interfejsov-v-linux/
https://winitpro.ru/index.php/2019/11/07/nastrojka-vlan-v-linux/

Оказывается задать виртуальный локальную сеть VLAN можно различными способами.
Я пробовал способ настройки конфигурации интерфейсов в файле /etc/network/interfaces на виртуальной машине с Ubuntu 20.04
с перезапуском службы (источник https://itproffi.ru/nastrojka-vlan-interfejsov-v-linux/ "Конфигурация VLAN в Debian и Ubuntu")

~$ ip a
1: lo: <LOOPBACK,UP,LOWER_UP> mtu 65536 qdisc noqueue state UNKNOWN group default qlen 1000
    link/loopback 00:00:00:00:00:00 brd 00:00:00:00:00:00
    inet 127.0.0.1/8 scope host lo
       valid_lft forever preferred_lft forever
    inet6 ::1/128 scope host
       valid_lft forever preferred_lft forever
2: eth0: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc fq_codel state UP group default qlen 1000
    link/ether 08:00:27:73:60:cf brd ff:ff:ff:ff:ff:ff
    inet 10.0.2.15/24 brd 10.0.2.255 scope global dynamic eth0
       valid_lft 86199sec preferred_lft 86199sec
    inet6 fe80::a00:27ff:fe73:60cf/64 scope link
       valid_lft forever preferred_lft forever
       
vagrant@vagrant:~$ sudo nano /etc/network/interfaces

        ##vlan с ID-100 для интерфейса eth0 with ID - 100 в Debian/Ubuntu Linux##
        auto eth0.100
        iface eth0.100 inet static
        address 10.0.2.200
        netmask 255.255.255.0
        vlan-raw-device eth0

$ systemctl list-unit-files | grep -i network
networkd-dispatcher.service            enabled         enabled
networking.service                     enabled         enabled
systemd-network-generator.service      disabled        enabled
systemd-networkd-wait-online.service   enabled-runtime enabled
systemd-networkd.service               enabled-runtime enabled
systemd-networkd.socket                disabled        enabled
network-online.target                  static          enabled
network-pre.target                     static          disabled
network.target                         static          disabled

vagrant@vagrant:~$ sudo systemctl restart networking

vagrant@vagrant:~$ ip a
1: lo: <LOOPBACK,UP,LOWER_UP> mtu 65536 qdisc noqueue state UNKNOWN group default qlen 1000
    link/loopback 00:00:00:00:00:00 brd 00:00:00:00:00:00
    inet 127.0.0.1/8 scope host lo
       valid_lft forever preferred_lft forever
    inet6 ::1/128 scope host
       valid_lft forever preferred_lft forever
2: eth0: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc fq_codel state UP group default qlen 1000
    link/ether 08:00:27:73:60:cf brd ff:ff:ff:ff:ff:ff
    inet 10.0.2.15/24 brd 10.0.2.255 scope global dynamic eth0
       valid_lft 85907sec preferred_lft 85907sec
    inet6 fe80::a00:27ff:fe73:60cf/64 scope link
       valid_lft forever preferred_lft forever
3: eth0.100@eth0: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc noqueue state UP group default qlen 1000
    link/ether 08:00:27:73:60:cf brd ff:ff:ff:ff:ff:ff
    inet 10.0.2.200/24 brd 10.0.2.255 scope global eth0.100
       valid_lft forever preferred_lft forever
    inet6 fe80::a00:27ff:fe73:60cf/64 scope link
       valid_lft forever preferred_lft forever

Вот он запущенный VLAN: 3: eth0.100@eth0: <BROADCAST,MULTICAST,UP,LOWER_UP>
```

4. Какие типы агрегации интерфейсов есть в Linux? Какие опции есть для балансировки нагрузки? Приведите пример конфига.
```
LAG агрегация портов позволяет использовать несколько физических интерфейсов как один логический, такое объединение позволяет увеличивать пропускную способность и надежность канала.
Типы LAG:
● статический (на Cisco mode on);
● динамический – LACP протокол (на Cisco mode active).

Опции для балансировки нагрузки интерфейсов в Linux:
mode=0 (balance-rr)
Этот режим используется по-умолчанию, если в настройках не указано другое. balance-rr обеспечивает балансировку нагрузки и отказоустойчивость. В данном режиме пакеты отправляются "по кругу" от первого интерфейса к последнему и сначала. Если выходит из строя один из интерфейсов, пакеты отправляются на остальные оставшиеся.При подключении портов к разным коммутаторам, требует их настройки.
mode=1 (active-backup)
При active-backup один интерфейс работает в активном режиме, остальные в ожидающем. Если активный падает, управление передается одному из ожидающих. Не требует поддержки данной функциональности от коммутатора.
mode=2 (balance-xor)
Передача пакетов распределяется между объединенными интерфейсами по формуле ((MAC-адрес источника) XOR (MAC-адрес получателя)) % число интерфейсов. Один и тот же интерфейс работает с определённым получателем. Режим даёт балансировку нагрузки и отказоустойчивость.
mode=3 (broadcast)
Происходит передача во все объединенные интерфейсы, обеспечивая отказоустойчивость.
mode=4 (802.3ad)
Это динамическое объединение портов. В данном режиме можно получить значительное увеличение пропускной способности как входящего так и исходящего трафика, используя все объединенные интерфейсы. Требует поддержки режима от коммутатора, а так же (иногда) дополнительную настройку коммутатора.
mode=5 (balance-tlb)
Адаптивная балансировка нагрузки. При balance-tlb входящий трафик получается только активным интерфейсом, исходящий - распределяется в зависимости от текущей загрузки каждого интерфейса. Обеспечивается отказоустойчивость и распределение нагрузки исходящего трафика. Не требует специальной поддержки коммутатора.
mode=6 (balance-alb)
Адаптивная балансировка нагрузки (более совершенная). Обеспечивает балансировку нагрузки как исходящего (TLB, transmit load balancing), так и входящего трафика (для IPv4 через ARP). Не требует специальной поддержки коммутатором, но требует возможности изменять MAC-адрес устройства.
(источник https://merk.ucoz.ru/publ/linux/set/bonding_tipy_agregacii_obedinenija_interfejsov_v_linux/4-1-0-2)

Configuration - Example 1
Modify the /etc/network/interfaces file:
auto bond0
iface bond0 inet static
    address 10.31.1.5
    netmask 255.255.255.0
    network 10.31.1.0
    gateway 10.31.1.254
    bond-slaves eth0 eth1
    bond-mode active-backup
    bond-miimon 100
    bond-downdelay 200
    bond-updelay 200
(источник https://wiki.debian.org/Bonding)
```

5. Сколько IP адресов в сети с маской /29 ? Сколько /29 подсетей можно получить из сети с маской /24. Приведите несколько примеров /29 подсетей внутри сети 10.10.10.0/24.
```
1) с маской /29 - всего 8 (узловых 6)  
2) /24 бит	255.255.255.0	254 хостов /29 бит	255.255.255.248	- 6 хостов 
Если перевести в двоичной системе, тогда 4-й октет
для /29 будет - 1111 1000
для /24 будет - 000 0000
логическое и дает результат 2^5 = 32

Ответ: 32 подсетей типа /29 можно получить внутри сети 10.10.10.0/24.

3)  ipcalc 10.10.10.0/24
Address:   10.10.10.0           00001010.00001010.00001010. 00000000
Netmask:   255.255.255.0 = 24   11111111.11111111.11111111. 00000000
Wildcard:  0.0.0.255            00000000.00000000.00000000. 11111111
=>
Network:   10.10.10.0/24        00001010.00001010.00001010. 00000000
HostMin:   10.10.10.1           00001010.00001010.00001010. 00000001
HostMax:   10.10.10.254         00001010.00001010.00001010. 11111110
Broadcast: 10.10.10.255         00001010.00001010.00001010. 11111111
Hosts/Net: 254                   Class A, Private Internet

/29 это по 6 хостов, значит:
10.10.10.7, 10.10.10.13
```

6. Задача: вас попросили организовать стык между 2-мя организациями. Диапазоны 10.0.0.0/8, 172.16.0.0/12, 192.168.0.0/16 уже заняты. Из какой подсети допустимо взять частные IP адреса? Маску выберите из расчета максимум 40-50 хостов внутри подсети.
```
$ ipcalc 10.0.0.0/8
Address:   10.0.0.0             00001010. 00000000.00000000.00000000
Netmask:   255.0.0.0 = 8        11111111. 00000000.00000000.00000000
Wildcard:  0.255.255.255        00000000. 11111111.11111111.11111111
=>
Network:   10.0.0.0/8           00001010. 00000000.00000000.00000000
HostMin:   10.0.0.1             00001010. 00000000.00000000.00000001
HostMax:   10.255.255.254       00001010. 11111111.11111111.11111110
Broadcast: 10.255.255.255       00001010. 11111111.11111111.11111111
Hosts/Net: 16777214              Class A, Private Internet

$ ipcalc 172.16.0.0/12
Address:   172.16.0.0           10101100.0001 0000.00000000.00000000
Netmask:   255.240.0.0 = 12     11111111.1111 0000.00000000.00000000
Wildcard:  0.15.255.255         00000000.0000 1111.11111111.11111111
=>
Network:   172.16.0.0/12        10101100.0001 0000.00000000.00000000
HostMin:   172.16.0.1           10101100.0001 0000.00000000.00000001
HostMax:   172.31.255.254       10101100.0001 1111.11111111.11111110
Broadcast: 172.31.255.255       10101100.0001 1111.11111111.11111111
Hosts/Net: 1048574               Class B, Private Internet

$ ipcalc 192.168.0.0/16
Address:   192.168.0.0          11000000.10101000. 00000000.00000000
Netmask:   255.255.0.0 = 16     11111111.11111111. 00000000.00000000
Wildcard:  0.0.255.255          00000000.00000000. 11111111.11111111
=>
Network:   192.168.0.0/16       11000000.10101000. 00000000.00000000
HostMin:   192.168.0.1          11000000.10101000. 00000000.00000001
HostMax:   192.168.255.254      11000000.10101000. 11111111.11111110
Broadcast: 192.168.255.255      11000000.10101000. 11111111.11111111
Hosts/Net: 65534                 Class C, Private Internet

Есть еще частные подсети в диапазоне
100.64.0.0 — 100.127.255.255 (маска подсети: 255.192.0.0 или /10
~$ ipcalc 100.64.0.0/10
Address:   100.64.0.0           01100100.01 000000.00000000.00000000
Netmask:   255.192.0.0 = 10     11111111.11 000000.00000000.00000000
Wildcard:  0.63.255.255         00000000.00 111111.11111111.11111111
=>
Network:   100.64.0.0/10        01100100.01 000000.00000000.00000000
HostMin:   100.64.0.1           01100100.01 000000.00000000.00000001
HostMax:   100.127.255.254      01100100.01 111111.11111111.11111110
Broadcast: 100.127.255.255      01100100.01 111111.11111111.11111111
Hosts/Net: 4194302               Class A

100.64.0.0 c маской 255.255.255.200

Из подсети 100.64.0.0 — 100.127.255.255 (маска подсети 255.192.0.0 или /10) - Данная подсеть рекомендована согласно RFC 6598 для использования в качестве адресов для CGN (Carrier-Grade NAT).
https://ru.wikipedia.org/wiki/%D0%A7%D0%B0%D1%81%D1%82%D0%BD%D1%8B%D0%B9_IP-%D0%B0%D0%B4%D1%80%D0%B5%D1%81
```

7. Как проверить ARP таблицу в Linux, Windows? Как очистить ARP кеш полностью? Как из ARP таблицы удалить только один нужный IP?
```
ARP-таблица - это соответствие IP-адресов и MAC-адресов устройств, находящихся в сети.
В Windows и Linux посмотреть ARP таблицу можно так:
C:\WINDOWS\system32>arp -a
Интерфейс: 192.168.0.102 --- 0x6
  адрес в Интернете      Физический адрес      Тип
  192.168.0.1           30-b5-c2-69-ca-f2     динамический
  192.168.0.255         ff-ff-ff-ff-ff-ff     статический
  224.0.0.2             01-00-5e-00-00-02     статический
  224.0.0.22            01-00-5e-00-00-16     статический
  224.0.0.251           01-00-5e-00-00-fb     статический
  224.0.0.252           01-00-5e-00-00-fc     статический
  239.255.255.250       01-00-5e-7f-ff-fa     статический
  255.255.255.255       ff-ff-ff-ff-ff-ff     статический
  
В Windows ARP кеш можно очистить командой 
netsh interface ip delete arpcache  

В Linux кеш можно очистить командой
$ ip -s -s neigh flush all

Удалить только один нужный IP можно с опцией -d
arp -d 192.168.1.1  
```
