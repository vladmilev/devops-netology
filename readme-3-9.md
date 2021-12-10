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
$ sudo nano /etc/apache2/sites-enabled/site

<VirtualHost *:443>
    ServerName mysite.ru
    DocumentRoot /var/www/html

    SSLEngine on
    SSLCertificateFile ssl/cert.pem
    SSLCertificateKeyFile ssl/cert.key
</VirtualHost>

На экране пусто - только буква R
Что не так?
```

4. Проверьте на TLS уязвимости произвольный сайт в интернете (кроме сайтов МВД, ФСБ, МинОбр, НацБанк, РосКосмос, РосАтом, РосНАНО и любых госкомпаний, объектов КИИ, ВПК ... и тому подобное).
```
собрал вирт.машину с Kali Linux содержащую популярные инструменты поиска уязвимостей
Vagrant.configure("2") do |config|
  config.vm.box = "kalilinux/rolling"
end
$ tlssled scanme.nmap.org
```

5. Установите на Ubuntu ssh сервер, сгенерируйте новый приватный ключ. Скопируйте свой публичный ключ на другой сервер. Подключитесь к серверу по SSH-ключу.
```
"Скопируйте свой публичный ключ на другой сервер. Подключитесь к серверу по SSH-ключу"
Подскажите пожалуйста как это можно сделать?
запустить две виртуальные машины?
как подключиться к серверу по SSH-ключу?
может есть статьи на эту тему?
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

