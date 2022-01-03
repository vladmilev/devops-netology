# Курсовая работа по итогам модуля "DevOps и системное администрирование"

1. Создайте виртуальную машину Linux.
```
 # Vagrantfile
 Vagrant.configure("2") do |config|
 	config.vm.box = "ubuntu/bionic64"
        config.vm.network "forwarded_port", guest:22, host:22
        config.vm.network "forwarded_port", guest:443, host:443
 end
```

2. Установите ufw и разрешите к этой машине сессии на порты 22 и 443, при этом трафик на интерфейсе localhost (lo) должен ходить свободно на все порты.
```
$ sudo apt install ufw 
$ sudo ufw allow ssh
$ sudo ufw allow https
$ sudo ufw enable
Command may disrupt existing ssh connections. Proceed with operation (y|n)? y
Firewall is active and enabled on system startup
$ sudo ufw status verbose
Status: active
Logging: on (low)
Default: deny (incoming), allow (outgoing), disabled (routed)
New profiles: skip

To                         Action      From
--                         ------      ----
22/tcp                     ALLOW IN    Anywhere
443/tcp                    ALLOW IN    Anywhere
22/tcp (v6)                ALLOW IN    Anywhere (v6)
443/tcp (v6)               ALLOW IN    Anywhere (v6)
```

3. Установите hashicorp vault (инструкция по ссылке).
```

```

4. Cоздайте центр сертификации по инструкции (ссылка) и выпустите сертификат для использования его в настройке веб-сервера nginx (срок жизни сертификата - месяц).
```

```

5. Установите корневой сертификат созданного центра сертификации в доверенные в хостовой системе.
```

```

6. Установите nginx.
```

```

7. По инструкции (ссылка) настройте nginx на https, используя ранее подготовленный сертификат:
можно использовать стандартную стартовую страницу nginx для демонстрации работы сервера;
можно использовать и другой html файл, сделанный вами;
```

```

8. Откройте в браузере на хосте https адрес страницы, которую обслуживает сервер nginx.
```

```

9. Создайте скрипт, который будет генерировать новый сертификат в vault:
генерируем новый сертификат так, чтобы не переписывать конфиг nginx;
перезапускаем nginx для применения нового сертификата.
```

```

10. Поместите скрипт в crontab, чтобы сертификат обновлялся какого-то числа каждого месяца в удобное для вас время.
```

```
