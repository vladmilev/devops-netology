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

3. Установите hashicorp vault (инструкция по ссылке https://learn.hashicorp.com/tutorials/vault/getting-started-install?in=vault/getting-started#install-vault).
```
$ curl -fsSL https://apt.releases.hashicorp.com/gpg | sudo apt-key add -
$ sudo apt-add-repository "deb [arch=amd64] https://apt.releases.hashicorp.com $(lsb_release -cs) main"
$ sudo apt-get update && sudo apt-get install vault
writing new private key to 'tls.key'
-----
Vault TLS key and self-signed certificate have been generated in '/opt/vault/tls'.
```

4. Cоздайте центр сертификации по инструкции (ссылка) и выпустите сертификат для использования его в настройке веб-сервера nginx (срок жизни сертификата - месяц).
```
В отдельном терминале запустил
$ vault server -dev -dev-root-token-id root
$ export VAULT_ADDR=http://127.0.0.1:8200
$ export VAULT_TOKEN=root
$ vault secrets enable pki
Success! Enabled the pki secrets engine at: pki/
$ vault secrets tune -max-lease-ttl=87600h pki
Success! Tuned the secrets engine at: pki/
$ vault write -field=certificate pki/root/generate/internal \
     common_name="example.com" \
     ttl=87600h > CA_cert.crt
$ vagrant@ubuntu-bionic:~$ vault write pki/config/urls \
      issuing_certificates="$VAULT_ADDR/v1/pki/ca" \
      crl_distribution_points="$VAULT_ADDR/v1/pki/crl"
Success! Data written to: pki/config/urls
$ vault secrets enable -path=pki_int pki
Success! Enabled the pki secrets engine at: pki_int/
$ vault secrets tune -max-lease-ttl=43800h pki_int
Success! Tuned the secrets engine at: pki_int/
$ sudo apt install jq
$ vault write -format=json pki_int/intermediate/generate/internal \
     common_name="example.com Intermediate Authority" \
     | jq -r '.data.csr' > pki_intermediate.csr
$ vault write -format=json pki/root/sign-intermediate csr=@pki_intermediate.csr \
     format=pem_bundle ttl="43800h" \
     | jq -r '.data.certificate' > intermediate.cert.pem
$ vault write pki_int/intermediate/set-signed certificate=@intermediate.cert.pem
Success! Data written to: pki_int/intermediate/set-signed

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
