## Домашнее задание к занятию "3.7. Компьютерные сети, лекция 2"

1. Проверьте список доступных сетевых интерфейсов на вашем компьютере. Какие команды есть для этого в Linux и в Windows?
```
Команда в Linux:
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

```

4. Какие типы агрегации интерфейсов есть в Linux? Какие опции есть для балансировки нагрузки? Приведите пример конфига.
```

```

5. Сколько IP адресов в сети с маской /29 ? Сколько /29 подсетей можно получить из сети с маской /24. Приведите несколько примеров /29 подсетей внутри сети 10.10.10.0/24.
```

```

6. Задача: вас попросили организовать стык между 2-мя организациями. Диапазоны 10.0.0.0/8, 172.16.0.0/12, 192.168.0.0/16 уже заняты. Из какой подсети допустимо взять частные IP адреса? Маску выберите из расчета максимум 40-50 хостов внутри подсети.
```

```

7. Как проверить ARP таблицу в Linux, Windows? Как очистить ARP кеш полностью? Как из ARP таблицы удалить только один нужный IP?
```

```
