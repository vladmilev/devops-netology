## Домашнее задание к занятию "3.9. Элементы безопасности информационных систем"

1. Установите Bitwarden плагин для браузера. Зарегистрируйтесь и сохраните несколько паролей.
```
Плагин для хрома
https://chrome.google.com/webstore/detail/bitwarden-free-password-m/nngceckbapebfimnlniiiahkandclblb/related?hl=ru
Сохранил пароли к репозиториям на гитхабе, гитлабе и баттрекере
```

2. Установите Google authenticator на мобильный телефон. Настройте вход в Bitwarden акаунт через Google authenticator OTP.

Скриншот запроса двухфакторной идентификации https://prnt.sc/22hcgtw

3. Установите apache2, сгенерируйте самоподписанный сертификат, настройте тестовый сайт для работы по HTTPS.

Скриншот установленного apache2 https://prnt.sc/22hjfwz
```
на старой виртуальной машине с ubuntu 20.04 устанавливаться не хотела, удалось поднять через новую виртуальную машину с
Vagrant.configure("2") do |config|
 	config.vm.box = "ubuntu/bionic64"
  config.vm.network "forwarded_port", guest:443, host:443
 end
 
настраивал самоподписание через https://www.dmosk.ru/miniinstruktions.php?mini=apache-ssl

$ cd /etc/apache2/ssl
$ sudo openssl req -new -x509 -days 1461 -nodes -out cert.pem -keyout cert.key -subj "/C=RU/ST=SPb/L=SPb/O=Global Security/OU=IT Department/CN=test.ru/CN=test"
Can't load /home/vagrant/.rnd into RNG
139645715239360:error:2406F079:random number generator:RAND_load_file:Cannot open file:../crypto/rand/randfile.c:88:Filename=/home/vagrant/.rnd
Generating a RSA private key
............+++++
.........+++++
writing new private key to 'cert.key'

Избавился от ошибки по https://stackoverflow.com/questions/63893662/cant-load-root-rnd-into-rng
> Try removing or commenting RANDFILE = $ENV::HOME/.rnd line in /etc/ssl/openssl.cnf

$ sudo openssl req -new -x509 -days 1461 -nodes -out cert.pem -keyout cert.key -subj "/C=RU/ST=SPb/L=SPb/O=Global Security/OU=IT Department/CN=test.ru/CN=test"
Generating a RSA private key
...................................................................+++++
..................+++++
writing new private key to 'cert.key'
-----

$ sudo nano /etc/apache2/sites-enabled/site

<VirtualHost *:443>
    ServerName test.ru
    DocumentRoot /var/www/html

    SSLEngine on
    SSLCertificateFile ssl/cert.pem
    SSLCertificateKeyFile ssl/cert.key
</VirtualHost>

$ sudo systemctl restart apache2

l$ apachectl configtest
Syntax OK

В браузере набираю https://test.ru
На экране - ошибка 
Не удается получить доступ к сайту
Проверьте, нет ли опечаток в имени хоста test.ru.
DNS_PROBE_FINISHED_NXDOMAIN

Команда apachectl configtest выдает ошибку
AH00558: apache2: Could not reliably determine the server's fully qualified domain name, using 127.0.1.1. Set the 'ServerName' directive globally to suppress this message

Удалось открыть сайт test.ru:443 только после того как прописал на windows-клиенте /windows/system32/drivers/etc/hosts
127.0.1.1 test.ru

через https://test.ru (без указания порта) - и как добиться такого варианта? - выдает ошибку ERR_SSL_PROTOCOL_ERROR
```
Скриншот https://prnt.sc/22rkeki

4. Проверьте на TLS уязвимости произвольный сайт в интернете (кроме сайтов МВД, ФСБ, МинОбр, НацБанк, РосКосмос, РосАтом, РосНАНО и любых госкомпаний, объектов КИИ, ВПК ... и тому подобное).
```
собрал вирт.машину с Kali Linux содержащую популярные инструменты поиска уязвимостей
Vagrant.configure("2") do |config|
  config.vm.box = "kalilinux/rolling"
end

┌──(vagrant㉿kali)-[~]
└─$ tlssled scanme.nmap.org 80
------------------------------------------------------
 TLSSLed - (1.3) based on sslscan and openssl
                 by Raul Siles (www.taddong.com)
------------------------------------------------------
    openssl version: OpenSSL 1.1.1l  24 Aug 2021
    
------------------------------------------------------
    Date: 20211209-224639
------------------------------------------------------

[*] Analyzing SSL/TLS on scanme.nmap.org:80 ...
    [.] Output directory: TLSSLed_1.3_scanme.nmap.org_80_20211209-224639 ...

[*] Checking if the target service speaks SSL/TLS...
    [.] The target service scanme.nmap.org:80 seems to speak SSL/TLS...

    [.] Using SSL/TLS protocol version: 
        (empty means I'm using the default openssl protocol version(s))

[*] Running sslscan on scanme.nmap.org:80 ...

    [-] Testing for SSLv2 ...

    [-] Testing for the NULL cipher ...

    [-] Testing for weak ciphers (based on key length - 40 or 56 bits) ...

    [+] Testing for strong ciphers (based on AES) ...

    [-] Testing for MD5 signed certificate ...

    [.] Testing for the certificate public key length ...

    [.] Testing for the certificate subject ...

    [.] Testing for the certificate CA issuer ...

    [.] Testing for the certificate validity period ...
    Today: Fri 10 Dec 2021 03:46:49 AM UTC

    [.] Checking preferred server ciphers ...


[*] Testing for SSL/TLS renegotiation MitM vuln. (CVE-2009-3555) ...

    [+] Testing for secure renegotiation support (RFC 5746) ...
    Secure Renegotiation IS NOT supported

[*] Testing for SSL/TLS renegotiation DoS vuln. (CVE-2011-1473) ...

    [.] Testing for client initiated (CI) SSL/TLS renegotiation (insecure)...
    UNKNOWN

[*] Testing for client authentication using digital certificates ...

    SSL/TLS client certificate authentication IS NOT required

[*] Testing for TLS v1.1 and v1.2 (CVE-2011-3389 vuln. aka BEAST) ...

    [-] Testing for SSLv3 and TLSv1 support ...

    [+] Testing for RC4 in the prefered cipher(s) list ...

    [.] Testing for TLS v1.1 support ...
    TLS v1.1 IS NOT supported

    [.] Testing for TLS v1.2 support ...
    TLS v1.2 IS NOT supported

[*] Testing for HTTPS (SSL/TLS) security headers using HTTP/1.0 ...

    [+] Testing for HTTP Strict-Transport-Security (HSTS) header ...

    [+] Testing for cookies with the secure flag ...

    [-] Testing for cookies without the secure flag ...

[*] Testing for HTTPS (SSL/TLS) security headers using HTTP/1.1 & Host ...

    [+] Testing for HTTP Strict-Transport-Security (HSTS) header ...

    [+] Testing for cookies with the secure flag ...

    [-] Testing for cookies without the secure flag ...

[*] New files created:
    [.] Output directory: TLSSLed_1.3_scanme.nmap.org_80_20211209-224639 ...

openssl_HEAD_1.0_scanme.nmap.org_80_20211209-224639.err
openssl_HEAD_1.0_scanme.nmap.org_80_20211209-224639.log
openssl_HEAD_scanme.nmap.org_80_20211209-224639.err
openssl_HEAD_scanme.nmap.org_80_20211209-224639.log
openssl_RENEG_LEGACY_scanme.nmap.org_80_20211209-224639.err
openssl_RENEG_LEGACY_scanme.nmap.org_80_20211209-224639.log
openssl_RENEG_scanme.nmap.org_80_20211209-224639.err
openssl_RENEG_scanme.nmap.org_80_20211209-224639.log
sslscan_scanme.nmap.org_80_20211209-224639.log

[*] done

```

5. Установите на Ubuntu ssh сервер, сгенерируйте новый приватный ключ. Скопируйте свой публичный ключ на другой сервер. Подключитесь к серверу по SSH-ключу.
```
1) Сгенерируем пару ключей на Windows-клиенте
$ ssh-keygen
Generating public/private rsa key pair.
Enter file in which to save the key (/c/Users/79059/.ssh/id_rsa): /c/Users/79059/.ssh/test_rsa
Enter passphrase (empty for no passphrase): 123
Enter same passphrase again: 123
Your identification has been saved in /c/Users/79059/.ssh/test_rsa
Your public key has been saved in /c/Users/79059/.ssh/test_rsa.pub
The key fingerprint is:
SHA256:BQLKWI4VX4Yio/GGVgPok75LusoGFhGfUxrFkViry+Y 79059@LAPTOP-3V9GR47T
The key's randomart image is:
+---[RSA 3072]----+
|.o*o*B* .        |
|=Oo**=.. .       |
|=*B*o.    .      |
|o*o o    .       |
|o.o.    S        |
|.o. .            |
|o..+             |
|+oo              |
|*+.E             |
+----[SHA256]-----+

2) Копируем открытый ключ
$ ssh-copy-id -p 2222 vagrant@localhost

/usr/bin/ssh-copy-id: INFO: Source of key(s) to be installed: "/c/Users/79059/.ssh/id_rsa.pub"
The authenticity of host '[localhost]:2222 ([127.0.0.1]:2222)' can't be established.
ECDSA key fingerprint is SHA256:qhuPFuTTMpA+0yViGR/ZLtXBpcGo4mUtr2IN25CSmDw.
Are you sure you want to continue connecting (yes/no/[fingerprint])? y
Please type 'yes', 'no' or the fingerprint: yes
/usr/bin/ssh-copy-id: INFO: attempting to log in with the new key(s), to filter out any that are already installed
/usr/bin/ssh-copy-id: INFO: 1 key(s) remain to be installed -- if you are prompted now it is to install the new keys
vagrant@localhost: Permission denied (publickey).
Насколько понимаю, ничего не было сделано, наверное файл уже существует с таким именем и его невозможно переписать

SSH server на виртуальной машине установлен:
$ sudo apt search openssh-server
Sorting... Done
Full Text Search... Done
openssh-server/bionic-updates,bionic-security,now 1:7.6p1-4ubuntu0.5 amd64 [installed]
  secure shell (SSH) server, for secure access from remote machines

~/.ssh
$ cat id_rsa.pub | ssh vagrant@localhost -p 2222 'cat >> ~/.ssh/authorized_keys'
vagrant@localhost: Permission denied (publickey).

Изменение прав доступа к папке и файлу в ней - не помогло
 chmod 755 ~/.ssh
vagrant@ubuntu-bionic:~$ cd .ssh
vagrant@ubuntu-bionic:~/.ssh$ ls -lva
total 12
drwx------ 2 vagrant vagrant 4096 Dec 12 04:08 .
drwxr-xr-x 5 vagrant vagrant 4096 Dec 12 05:00 ..
-rw------- 1 vagrant vagrant  389 Dec 12 04:08 authorized_keys
vagrant@ubuntu-bionic:~/.ssh$ chmod 755 authorized_keys

Не понимаю почему невозможно подключиться по ssh
При загрузке vargant написано
    default: SSH address: 127.0.0.1:2222
    default: SSH username: vagrant
Но запуская на windows-клиенте 
$ ssh vagrant@127.0.0.1:2222
ssh: Could not resolve hostname 127.0.0.1:2222: Name or service not known
79059@LAPTOP-3V9GR47T MINGW64 ~/.ssh
$ ssh vagrant@127.0.0.1:22
ssh: Could not resolve hostname 127.0.0.1:22: Name or service not known
- получаю ошибку

```

6. Переименуйте файлы ключей из задания 5. Настройте файл конфигурации SSH клиента, так чтобы вход на удаленный сервер осуществлялся по имени сервера.
```
...
```

7. Соберите дамп трафика утилитой tcpdump в формате pcap, 100 пакетов. Откройте файл pcap в Wireshark.
```
$ sudo tcpdump -c 100 -w 0001.pcap -i eth0 
tcpdump: listening on eth0, link-type EN10MB (Ethernet), snapshot length 262144 bytes
100 packets captured
199 packets received by filter
0 packets dropped by kernel
```
Скриншот Wireshark с открытым файлом https://prnt.sc/22jpb8d


Задание для самостоятельной отработки (необязательно к выполнению)  
8*. Просканируйте хост scanme.nmap.org. Какие сервисы запущены?  
9*. Установите и настройте фаервол ufw на web-сервер из задания 3. Откройте доступ снаружи только к портам 22,80,443  

