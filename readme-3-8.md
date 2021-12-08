## Домашнее задание к занятию "3.8. Компьютерные сети, лекция 3"

1. Подключитесь к публичному маршрутизатору в интернет. Найдите маршрут к вашему публичному IP
```
telnet route-views.routeviews.org
Username: rviews
show ip route x.x.x.x/32
show bgp x.x.x.x/32
```
Ответ:  
```
route-views>show ip route 45.84.224.58  
Routing entry for 45.84.224.0/24
  Known via "bgp 6447", distance 20, metric 0
  Tag 3267, type external
  Last update from 194.85.40.15 1w1d ago
  Routing Descriptor Blocks:
  * 194.85.40.15, from 194.85.40.15, 1w1d ago
      Route metric is 0, traffic share count is 1
      AS Hops 2
      Route tag 3267
      MPLS label: none
```

2. Создайте dummy0 интерфейс в Ubuntu. Добавьте несколько статических маршрутов. Проверьте таблицу маршрутизации.
```
# sudo -i
# ip link add dummy0 type dummy
# ip route add 192.168.2.0/24 via 10.0.2.15
# ip route add 192.168.3.159/32 via 10.0.2.15
# ip route show
default via 10.0.2.2 dev eth0 proto dhcp src 10.0.2.15 metric 100
10.0.2.0/24 dev eth0.100 proto kernel scope link src 10.0.2.200
10.0.2.0/24 dev eth0 proto kernel scope link src 10.0.2.15
10.0.2.2 dev eth0 proto dhcp scope link src 10.0.2.15 metric 100
192.168.2.0/24 via 10.0.2.15 dev eth0
192.168.3.159 via 10.0.2.15 dev eth0

```

3. Проверьте открытые TCP порты в Ubuntu, какие протоколы и приложения используют эти порты? Приведите несколько примеров.
```
$ sudo netstat -ntlp
Активные соединения с интернетом (only servers)
Proto Recv-Q Send-Q Local Address Foreign Address State       PID/Program name    
tcp        0      0 127.0.0.53:53           0.0.0.0:*               LISTEN      537/systemd-resolve
tcp        0      0 127.0.0.1:631           0.0.0.0:*               LISTEN      6840/cupsd          
tcp        0      0 127.0.0.1:5432          0.0.0.0:*               LISTEN      843/postgres        
tcp        0      0 127.0.0.1:9000          0.0.0.0:*               LISTEN      718/php-fpm: master
tcp        0      0 127.0.0.1:27017         0.0.0.0:*               LISTEN      7157/mongod        
tcp        0      0 127.0.0.1:6379          0.0.0.0:*               LISTEN      720/redis-server 12
tcp        0      0 0.0.0.0:655             0.0.0.0:*               LISTEN      7205/tincd          
tcp        0      0 0.0.0.0:80              0.0.0.0:*               LISTEN      830/nginx: master p
tcp6       0      0 ::1:631                 :::*                    LISTEN      6840/cupsd          
tcp6       0      0 ::1:5432                :::*                    LISTEN      843/postgres        
tcp6       0      0 127.0.0.1:6942          :::*                    LISTEN      7552/java          
tcp6       0      0 ::1:6379                :::*                    LISTEN      720/redis-server 12
tcp6       0      0 127.0.0.1:63342         :::*                    LISTEN      7552/java          
tcp6       0      0 :::655                  :::*                    LISTEN      7205/tincd          
tcp6       0      0 :::80                   :::*                    LISTEN      830/nginx: master p

Так для приложения nginx видим (последняя строчка) протокол tcp6 и порт 80
postgres - протокол tcp и порт 5432
```

4. Проверьте используемые UDP сокеты в Ubuntu, какие протоколы и приложения используют эти порты?
```
$ sudo ss -nulp
State       Recv-Q       Send-Q             Local Address:Port              Peer Address:Port      Process                                                             
UNCONN      0            0                        0.0.0.0:59965                  0.0.0.0:*          users:(("GeckoMain",pid=7263,fd=64))                               
UNCONN      0            0                  127.0.0.53%lo:53                     0.0.0.0:*          users:(("systemd-resolve",pid=537,fd=12))                         
UNCONN      0            0                      127.0.0.1:161                    0.0.0.0:*          users:(("snmpd",pid=726,fd=6))                                     
UNCONN      0            0                        0.0.0.0:631                    0.0.0.0:*          users:(("cups-browsed",pid=6843,fd=7))                             
UNCONN      0            0                        0.0.0.0:655                    0.0.0.0:*          users:(("tincd",pid=7205,fd=9))                                   
UNCONN      0            0                        0.0.0.0:5353                   0.0.0.0:*          users:(("avahi-daemon",pid=583,fd=12))                             
UNCONN      0            0                        0.0.0.0:55490                  0.0.0.0:*          users:(("avahi-daemon",pid=583,fd=14))                             
UNCONN      0            0                          [::1]:161                       [::]:*          users:(("snmpd",pid=726,fd=7))                                     
UNCONN      0            0                           [::]:655                       [::]:*          users:(("tincd",pid=7205,fd=11))                                   
UNCONN      0            0                           [::]:5353                      [::]:*          users:(("avahi-daemon",pid=583,fd=13))                             
UNCONN      0            0                           [::]:59227                     [::]:*          users:(("avahi-daemon",pid=583,fd=15))

Опция -u указывает на показ протокола UDP, процессы (приложения) использующие этот протокол указаны в последнем столбце, порты указаны в столбце Local Address:Port
```

5. Используя diagrams.net, создайте L3 диаграмму вашей домашней сети или любой другой сети, с которой вы работали.
```
Создал диаграмму, добавив ее в репозиторий на гитхабе
https://app.diagrams.net/?mode=github#Hvladmilev%2Fdevops-netology%2Fmain%2Fhome-network.drawio
```
![Диаграмма домашней сети](https://github.com/vladmilev/devops-netology/blob/main/home-network.drawio.png)
```
L3 уровень получим если добавим информацию по IP-адресам (маршрутизатора, остальные раздаются динамически)
```

### Задание для самостоятельной отработки (необязательно к выполнению)

6*. Установите Nginx, настройте в режиме балансировщика TCP или UDP.
```
Установил по источнику https://nginx.org/ru/docs/install.html

Балансировщик настраивается в конфигурационном файле /etc/nginx/nginx.conf

http {
    upstream myapp1 {
        server srv1.example.com;
        server srv2.example.com;
        server srv3.example.com;
    }

    server {
        listen 80;

        location / {
            proxy_pass http://myapp1;
        }
    }
}

По источнику http://onreader.mdl.ru/NGINXCookbook/content/Ch02.html
stream {
    upstream mysql_read {
        server read1.example.com:3306 weight=5;
        server read2.example.com:3306;
        server 10.10.12.34:3306 backup;
    }

    server {
        listen 3306;
        proxy_pass mysql_read;
    }
}
Это для TCP, а для UDP:  

stream {
    upstream ntp {
        server ntp1.example.com:123 weight=2;
        server ntp2.example.com:123;
    }

    server {
        listen 123 udp;
        proxy_pass ntp;
    }
}
используется параметр udp
```

7*. Установите bird2, настройте динамический протокол маршрутизации RIP.
```
устанавливал по https://bird.network.cz/pipermail/bird-users/2020-August/014770.html
выдает ошибку
E: Failed to fetch http://security.ubuntu.com/ubuntu/pool/main/libs/libssh/libssh-gcrypt-4_0.9.3-2ubuntu2.1_amd64.deb  404  Not Found [IP: 91.189.88.142 80]
```

8*. Установите Netbox, создайте несколько IP префиксов, используя curl проверьте работу API.
```
пока не пробовал, нашел источник https://jtprog.ru/netbox/
```
