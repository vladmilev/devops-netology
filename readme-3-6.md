## Домашнее задание к занятию "3.6. Компьютерные сети, лекция 1"

1. Работа c HTTP через телнет.  
Подключитесь утилитой телнет к сайту stackoverflow.com: telnet stackoverflow.com 80  
отправьте HTTP запрос  
```
GET /questions HTTP/1.0
HOST: stackoverflow.com
[press enter]
[press enter]
```
В ответе укажите полученный HTTP код, что он означает?  
```
HTTP/1.1 400 Bad Request
Connection: close
Content-Length: 0

Connection closed by foreign host.

Код состояния ответа "HTTP 400 Bad Request" указывает, что сервер не смог понять запрос из-за недействительного синтаксиса. Клиент не должен повторять этот запрос без изменений.
```

2. Повторите задание 1 в браузере, используя консоль разработчика F12.  
откройте вкладку Network   
отправьте запрос http://stackoverflow.com  
найдите первый ответ HTTP сервера, откройте вкладку Headers  
укажите в ответе полученный HTTP код.  
проверьте время загрузки страницы, какой запрос обрабатывался дольше всего?  
приложите скриншот консоли браузера в ответ.  
```
307 Internal Redirect
Дольше всего обрабатывался GET-запрос на адрес https://stackoverflow.com/
Скриншет браузера по ссылке https://prnt.sc/20y2mso
```

3. Какой IP адрес у вас в интернете?  
```
hostname -I
192.168.0.107

ip a
1: lo: <LOOPBACK,UP,LOWER_UP> mtu 65536 qdisc noqueue state UNKNOWN group default qlen 1000
    link/loopback 00:00:00:00:00:00 brd 00:00:00:00:00:00
    inet 127.0.0.1/8 scope host lo
       valid_lft forever preferred_lft forever
    inet6 ::1/128 scope host
       valid_lft forever preferred_lft forever
2: enp3s0: <NO-CARRIER,BROADCAST,MULTICAST,UP> mtu 1500 qdisc fq_codel state DOWN group default qlen 1000
    link/ether b4:a9:fc:5f:5d:00 brd ff:ff:ff:ff:ff:ff
3: wlp4s0: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc noqueue state UP group default qlen 1000
    link/ether 10:63:c8:cd:b5:7f brd ff:ff:ff:ff:ff:ff
    inet 192.168.0.107/24 brd 192.168.0.255 scope global dynamic noprefixroute wlp4s0
       valid_lft 6723sec preferred_lft 6723sec
    inet6 fe80::31c9:51d5:9169:848d/64 scope link noprefixroute
       valid_lft forever preferred_lft forever
       
Мой IP адрес 192.168.0.107
```

4. Какому провайдеру принадлежит ваш IP адрес? Какой автономной системе AS? Воспользуйтесь утилитой whois  
```
$ whois 192.168.0.107

NetRange:       192.168.0.0 - 192.168.255.255
CIDR:           192.168.0.0/16
NetName:        PRIVATE-ADDRESS-CBLK-RFC1918-IANA-RESERVED
NetHandle:      NET-192-168-0-0-1
Parent:         NET192 (NET-192-0-0-0-0)
NetType:        IANA Special Use
OriginAS:      
Organization:   Internet Assigned Numbers Authority (IANA)
RegDate:        1994-03-15
Updated:        2013-08-30
Comment:        These addresses are in use by many millions of independently operated networks, which might be as small as a single computer connected to a home gateway, and are automatically configured in hundreds of millions of devices.  They are only intended for use within a private context  and traffic that needs to cross the Internet will need to use a different, unique address.
Comment:        
Comment:        These addresses can be used by anyone without any need to coordinate with IANA or an Internet registry.  The traffic from these addresses does not come from ICANN or IANA.  We are not the source of activity you may see on logs or in e-mail records.  Please refer to http://www.iana.org/abuse/answers
Comment:        
Comment:        These addresses were assigned by the IETF, the organization that develops Internet protocols, in the Best Current Practice document, RFC 1918 which can be found at:
Comment:        http://datatracker.ietf.org/doc/rfc1918
Ref:            https://rdap.arin.net/registry/ip/192.168.0.0

OrgName:        Internet Assigned Numbers Authority
OrgId:          IANA
Address:        12025 Waterfront Drive
Address:        Suite 300
City:           Los Angeles
StateProv:      CA
PostalCode:     90292
Country:        US
RegDate:        
Updated:        2012-08-31
Ref:            https://rdap.arin.net/registry/entity/IANA

OrgTechHandle: IANA-IP-ARIN
OrgTechName:   ICANN
OrgTechPhone:  +1-310-301-5820
OrgTechEmail:  abuse@iana.org
OrgTechRef:    https://rdap.arin.net/registry/entity/IANA-IP-ARIN

OrgAbuseHandle: IANA-IP-ARIN
OrgAbuseName:   ICANN
OrgAbusePhone:  +1-310-301-5820
OrgAbuseEmail:  abuse@iana.org
OrgAbuseRef:    https://rdap.arin.net/registry/entity/IANA-IP-ARIN

$ whois -h whois.radb.net 192.168.0.107
%  No entries found for the selected source(s).
```

5. Через какие сети проходит пакет, отправленный с вашего компьютера на адрес 8.8.8.8? Через какие AS? Воспользуйтесь утилитой traceroute  
```
$ traceroute -AU 8.8.8.8
traceroute to 8.8.8.8 (8.8.8.8), 30 hops max, 60 byte packets
 1  _gateway (192.168.0.1) [*]  2.909 ms  4.045 ms  4.003 ms
 2  128-69-128-1.broadband.corbina.ru (128.69.128.1) [AS8402]  4.263 ms  5.534 ms  5.502 ms
 3  78.107.181.108 (78.107.181.108) [AS8402]  8.700 ms  5.434 ms  8.635 ms
 4  195.222.164.233 (195.222.164.233) [AS3216]  8.604 ms  8.572 ms  8.539 ms
 5  79.104.238.45 (79.104.238.45) [AS3216]  57.843 ms 79.104.238.41 (79.104.238.41) [AS3216]  57.264 ms 79.104.238.45 (79.104.238.45) [AS3216]  57.781 ms
 6  218.100.6.173 (218.100.6.173) [AS23640]  90.805 ms  95.226 ms 218.100.6.53 (218.100.6.53) [AS23640]  88.110 ms
 7  108.170.242.161 (108.170.242.161) [AS15169]  89.210 ms  88.602 ms 108.170.242.97 (108.170.242.97) [AS15169]  89.078 ms
 8  108.170.227.93 (108.170.227.93) [AS15169]  90.158 ms 142.251.226.139 (142.251.226.139) [AS15169]  90.125 ms 209.85.253.57 (209.85.253.57) [AS15169]  90.091 ms
 9  * * *
10  * * *
11  * * *
12  * * *
13  * * *
14  * * *
15  * * *
16  * * *
17  * * *
18  * * *
19  * * *
20  * * *
21  * * *
22  * * *
23  * * *
24  * * *
25  * * *
26  * * *
27  * * *
28  * * *
29  * * *
30  * * *
```

6. Повторите задание 5 в утилите mtr. На каком участке наибольшая задержка - delay?  
```
https://prnt.sc/2121wxu
В среднем наибольшая задержка на AS15169 c ip 108.170.242.97
```

7. Какие DNS сервера отвечают за доменное имя dns.google? Какие A записи? воспользуйтесь утилитой dig  
```
$ dig dns.google

; <<>> DiG 9.16.8-Ubuntu <<>> dns.google
;; global options: +cmd
;; Got answer:
;; ->>HEADER<<- opcode: QUERY, status: NOERROR, id: 2007
;; flags: qr rd ra; QUERY: 1, ANSWER: 2, AUTHORITY: 0, ADDITIONAL: 1

;; OPT PSEUDOSECTION:
; EDNS: version: 0, flags:; udp: 65494
;; QUESTION SECTION:
;dns.google. IN A

;; ANSWER SECTION:
dns.google. 2508 IN A 8.8.4.4
dns.google. 2508 IN A 8.8.8.8

;; Query time: 60 msec
;; SERVER: 127.0.0.53#53(127.0.0.53)
;; WHEN: Пн ноя 29 13:34:38 +07 2021
;; MSG SIZE  rcvd: 71

dns.google указывает на A записи:
A 8.8.4.4
A 8.8.8.8
```

8. Проверьте PTR записи для IP адресов из задания 7. Какое доменное имя привязано к IP? воспользуйтесь утилитой dig  
```

```
