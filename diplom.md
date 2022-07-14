# Дипломный практикум в YandexCloud

## 1. Доменное имя
- зарегистрирован домен `milevsky.quest` через регистратора https://www.reg.ru/  
- добавлены настройки для субдоменов (А-записи, указывают соответствие доменного имени и IPv4-адреса) 
```
https://www.milevsky.quest (WordPress) www → 194.58.112.174
https://gitlab.milevsky.quest (Gitlab) gitlab → 194.58.112.174
https://grafana.milevsky.quest (Grafana) grafana → 194.58.112.174
https://prometheus.milevsky.quest (Prometheus) prometheus → 194.58.112.174
https://alertmanager.milevsky.quest (Alert Manager) alertmanager → 194.58.112.174
```
Личный кабинет https://www.reg.ru/user/account 
>![PID 1](../img/dns.png)  

## 2. Облачная инфраструктура 
(развертывание инфраструктуры с помощью Terraform на базе облачного провайдера YandexCloud)

На виртуальной машине (may1) уже оказались установлены yc и terraform  
Настройку бакета (бекэнда) делал по [Загрузка состояний Terraform в Object Storage](https://cloud.yandex.ru/docs/tutorials/infrastructure-management/terraform-state-storage)  

предварительно - создал сервис-аккаунт:  
$ yc iam service-account create --name vlad-milev-account --description "favorite service account"  
id: aje1sgffja87k3fi62gj  
назначил созданному сервисному аккаунту (aje1sgffja87k3fi62gj) роль editor  
Создал статические ключи доступа:  
$ yc iam access-key create --service-account-name vlad-milev-account --description "this key is for my bucket"   
Через консоль console.cloud.yandex.ru создал бакет vlad-milev-bucket   
Можно писать и запускать код - предварительно создам новый репозиторий (под диплом) https://github.com/vladmilev/diplom   
$ git clone git@github.com:vladmilev/diplom.git

Настройки провайдера и бэкенда - providers.tf  
Настройки сети и двух подсетей в разных зонах доступности - network.tf  
Инициализируем terrraform
$ terraform init
Создаем воркспейсы stage и prod:  
$ terraform workspace new stage  
$ terraform workspace new prod  
(долго выдавала ошибку доступа - надо вызвать ACL-бакета через три точки в списке бакетов консоли в браузере)  
указанные воркспейсы появились в бакете облака:  
>![PID 1](../img/workspaces.png)  

$ terraform apply  
создает (на данном этапе) 1 сеть (network-1) с двумя подсетями (subnet-1, subnet-2)  
$ terraform destroy  
удаляет указанные ресурсы из облака  

## 3. Nginx и LetsEncrypt  
(Ansible роль для установки Nginx и LetsEncrypt)  
Необходимо создать reverse proxy с поддержкой TLS для обеспечения безопасного доступа к веб-сервисам по HTTPS.  

Reverse proxy (обратный прокси-сервер) — тип прокси-сервера, который ретранслирует запросы клиентов из внешней сети на один или несколько серверов, логически расположенных во внутренней сети. При этом для клиента это выглядит так, будто запрашиваемые ресурсы находятся непосредственно на прокси-сервере).  

Надо поднять одну виртуальную машину с сервером nginx (194.58.112.174 - адрес, доменное имя milevsky.quest) c характеристиками: 2vCPU, 2 RAM, External address (Public) и Internal address.   
Сервер с Nginx будет служить маршрутиразтором запросов к различным серверам внутренней сети - у нас есть только один сервер с public ip (194.58.112.174), а сертификаты LetsEncrypt нужны на серверах внутри сети для безопасного трафика.  

Поиск по запросу: Ansible Nginx и letsencrypt выдал первую ссылку с решением https://gist.github.com/mattiaslundberg/ba214a35060d3c8603e9b1ec8627d349  

1. В вашей доменной зоне настроены все A-записи на внешний адрес этого сервера:
Статья про Cloud DNS https://cloud.yandex.ru/docs/dns/quickstart

2. Поднимем ВМ под сервер Nginx - с помощью Terraform (terraform apply) - файл nginx.tf
```
resource "yandex_compute_instance" "nginx" {
  name     = "nginx"
  hostname = "milevsky.quest"

  resources {
    cores  = 2
    memory = 2
  }

  boot_disk {
    initialize_params {
      image_id = "fd8ic5bmgr51h6e7bh1v"
    }
  }

  network_interface {
    subnet_id      = yandex_vpc_subnet.subnet-1.id
    nat            = true
    nat_ip_address = var.yc_public_ip
  }

  metadata = {
    ssh-keys  = "ubuntu:${file("~/.ssh/id_rsa.pub")}"
  }
}
```
variables.tf  
```
variable "yc_public_ip" {
  default = "62.84.117.51"
}
```

3. указываем хост сервера в файле ansible/hosts  
```
[nginx]
milevsky.quest  letsencrypt_email=vlad_milev@mail.ru domain_name=milevsky.quest
```

4. формируем плей для выполнения ansible/nginx-server.yml
```
- hosts: nginx
  become: true
  gather_facts: true
  roles:
   - Nginx_LetsEncrypt
```   

5. Формируем роль Nginx_LetsEncrypt  
список задач ansible/roles/Nginx_LetsEncrypt/tasks/main.yml
```
---
- name: python-simplejson
  raw: apt-get install -y python-simplejson

- name: Upgrade system
  apt: update_cache=yes

- name: Install nginx
  apt: name=nginx state=latest

- name: install letsencrypt
  apt: name=letsencrypt state=latest

- name: create letsencrypt directory
  file: name=/var/www/letsencrypt state=directory

- name: Remove default nginx config
  file: name=/etc/nginx/sites-enabled/default state=absent

- name: Install system nginx config
  template:
    src: templates/nginx.conf.j2
    dest: /etc/nginx/nginx.conf

- name: Install nginx site for letsencrypt requests
  template:
    src: templates/nginx-http.j2
    dest: /etc/nginx/sites-enabled/http

- name: Reload nginx to activate letsencrypt site
  service: name=nginx state=restarted

- name: Create letsencrypt certificate front
  shell: letsencrypt certonly -n --webroot -w /var/www/letsencrypt -m {{ letsencrypt_email }} --agree-tos -d {{ domain_name }}
  args:
    creates: /etc/letsencrypt/live/{{ domain_name }}
  
- name: Generate dhparams
  shell: openssl dhparam -out /etc/nginx/dhparams.pem 2048
  args:
    creates: /etc/nginx/dhparams.pem

- name: Install nginx site for specified site
  template:
    src: templates/nginx-le.j2
    dest: /etc/nginx/sites-enabled/le

- name: Reload nginx to activate specified site
  service: name=nginx state=restarted

- name: Add letsencrypt cronjob for cert renewal
  cron:
    name: letsencrypt_renewal
    special_time: weekly
    job: letsencrypt --renew certonly -n --webroot -w /var/www/letsencrypt -m {{ letsencrypt_email }} --agree-tos -d {{ domain_name }} && service nginx reload
```
настройка для /etc/nginx/nginx.conf - в ansible/roles/Nginx_LetsEncrypt/templates/nginx.conf.j2
```
user www-data;
worker_processes 4;
pid /run/nginx.pid;

events {
    worker_connections 768;
}

http {

    sendfile on;
    tcp_nopush on;
    tcp_nodelay on;
    keepalive_timeout 65;
    types_hash_max_size 2048;
    include /etc/nginx/mime.types;
    default_type application/octet-stream;

    access_log /var/log/nginx/access.log;
    error_log /var/log/nginx/error.log;

    gzip on;
    gzip_disable "msie6";

    include /etc/nginx/conf.d/*.conf;
    include /etc/nginx/sites-enabled/*;
}
```
настройка для /etc/nginx/sites-enabled/http - в ansible/roles/Nginx_LetsEncrypt/templates/nginx-http.j2
```
server_tokens off;

server {
    listen 80;
    server_name {{ domain_name }};

    location /.well-known/acme-challenge {
        root /var/www/letsencrypt;
        try_files $uri $uri/ =404;
    }

    location / {
        rewrite ^ https://{{ domain_name }}$request_uri? permanent;
    }
}
```
настройка для /etc/nginx/sites-enabled/le - в ansible/roles/Nginx_LetsEncrypt/templates/nginx-le.j2
```
add_header X-Frame-Options SAMEORIGIN;
add_header X-Content-Type-Options nosniff;
add_header X-XSS-Protection "1; mode=block";
add_header Content-Security-Policy "default-src 'self'; script-src 'self' 'unsafe-inline' 'unsafe-eval' https://www.google-analytics.com; img-src 'self' data: https://www.google-analytics.com; style-src 'self' 'unsafe-inline'; font-src 'self'; frame-src 'none'; object-src 'none'";


# HTTPS server
#
server {
    listen 443 ssl;
    server_name {{ domain_name }};

    ssl on;
    ssl_certificate         /etc/letsencrypt/live/{{ domain_name }}/fullchain.pem;
    ssl_certificate_key     /etc/letsencrypt/live/{{ domain_name }}/privkey.pem;
    ssl_trusted_certificate /etc/letsencrypt/live/{{ domain_name }}/fullchain.pem;

    ssl_session_cache shared:SSL:50m;
    ssl_session_timeout 5m;
    ssl_stapling on;
    ssl_stapling_verify on;

    ssl_protocols TLSv1 TLSv1.1 TLSv1.2;
    ssl_ciphers "ECDHE-RSA-AES256-GCM-SHA384:ECDHE-RSA-AES128-GCM-SHA256:DHE-RSA-AES256-GCM-SHA384:DHE-RSA-AES128-GCM-SHA256:ECDHE-RSA-AES256-SHA384:ECDHE-RSA-AES128-SHA256:ECDHE-RSA-AES256-SHA:ECDHE-RSA-AES128-SHA:DHE-RSA-AES256-SHA256:DHE-RSA-AES128-SHA256:DHE-RSA-AES256-SHA:DHE-RSA-AES128-SHA:ECDHE-RSA-DES-CBC3-SHA:EDH-RSA-DES-CBC3-SHA:AES256-GCM-SHA384:AES128-GCM-SHA256:AES256-SHA256:AES128-SHA256:AES256-SHA:AES128-SHA:DES-CBC3-SHA:HIGH:!aNULL:!eNULL:!EXPORT:!DES:!MD5:!PSK:!RC4";

    ssl_dhparam /etc/nginx/dhparams.pem;
    ssl_prefer_server_ciphers on;

#    root /var/www/{{ domain_name }};
#    index index.html index.htm;

    location / {
#        try_files $uri $uri/ =404;
         proxy_pass http://app.milevsky.quest/;
         proxy_set_header Host $http_host;
         proxy_set_header X-Forwarded-Host $http_host;
         proxy_set_header X-Real-IP $remote_addr;
         proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
         proxy_set_header X-Forwarded-Proto https;
         proxy_set_header HTTPS YES;
    }
}
server {
    listen 80;

    server_name _;

  rewrite ^(.*) https://$host$1 permanent;
}
server {
    listen 443 ssl;
    server_name grafana.milevsky.quest;

    ssl on;
    ssl_certificate         /etc/letsencrypt/live/grafana.milevsky.quest/fullchain.pem;
    ssl_certificate_key     /etc/letsencrypt/live/grafana.milevsky.quest/privkey.pem;
    ssl_trusted_certificate /etc/letsencrypt/live/grafana.milevsky.quest/fullchain.pem;

    ssl_session_cache shared:SSL:50m;
    ssl_session_timeout 5m;
    ssl_stapling on;
    ssl_stapling_verify on;

    ssl_protocols TLSv1 TLSv1.1 TLSv1.2;
    ssl_ciphers "ECDHE-RSA-AES256-GCM-SHA384:ECDHE-RSA-AES128-GCM-SHA256:DHE-RSA-AES256-GCM-SHA384:DHE-RSA-AES128-GCM-SHA256:ECDHE-RSA-AES256-SHA384:ECDHE-RSA-AES128-SHA256:ECDHE-RSA-AES256-SHA:ECDHE-RSA-AES128-SHA:DHE-RSA-AES256-SHA256:DHE-RSA-AES128-SHA256:DHE-RSA-AES256-SHA:DHE-RSA-AES128-SHA:ECDHE-RSA-DES-CBC3-SHA:EDH-RSA-DES-CBC3-SHA:AES256-GCM-SHA384:AES128-GCM-SHA256:AES256-SHA256:AES128-SHA256:AES256-SHA:AES128-SHA:DES-CBC3-SHA:HIGH:!aNULL:!eNULL:!EXPORT:!DES:!MD5:!PSK:!RC4";

    ssl_dhparam /etc/nginx/dhparams.pem;
    ssl_prefer_server_ciphers on;
    location / {
#        try_files $uri $uri/ =404;
         proxy_pass http://monitoring.zhukops.ru:3000/;
         proxy_set_header Host $http_host;
         proxy_set_header X-Forwarded-Host $http_host;
         proxy_set_header X-Real-IP $remote_addr;
         proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
#         proxy_set_header X-Forwarded-Proto https;
#         proxy_set_header HTTPS YES;
    }
}
server {
    listen 443 ssl;
    server_name prometheus.zhukops.ru;

    ssl on;
    ssl_certificate         /etc/letsencrypt/live/prometheus.zhukops.ru/fullchain.pem;
    ssl_certificate_key     /etc/letsencrypt/live/prometheus.zhukops.ru/privkey.pem;
    ssl_trusted_certificate /etc/letsencrypt/live/prometheus.zhukops.ru/fullchain.pem;

    ssl_session_cache shared:SSL:50m;
    ssl_session_timeout 5m;
    ssl_stapling on;
    ssl_stapling_verify on;

    ssl_protocols TLSv1 TLSv1.1 TLSv1.2;
    ssl_ciphers "ECDHE-RSA-AES256-GCM-SHA384:ECDHE-RSA-AES128-GCM-SHA256:DHE-RSA-AES256-GCM-SHA384:DHE-RSA-AES128-GCM-SHA256:ECDHE-RSA-AES256-SHA384:ECDHE-RSA-AES128-SHA256:ECDHE-RSA-AES256-SHA:ECDHE-RSA-AES128-SHA:DHE-RSA-AES256-SHA256:DHE-RSA-AES128-SHA256:DHE-RSA-AES256-SHA:DHE-RSA-AES128-SHA:ECDHE-RSA-DES-CBC3-SHA:EDH-RSA-DES-CBC3-SHA:AES256-GCM-SHA384:AES128-GCM-SHA256:AES256-SHA256:AES128-SHA256:AES256-SHA:AES128-SHA:DES-CBC3-SHA:HIGH:!aNULL:!eNULL:!EXPORT:!DES:!MD5:!PSK:!RC4";

    ssl_dhparam /etc/nginx/dhparams.pem;
    ssl_prefer_server_ciphers on;
    location / {
#        try_files $uri $uri/ =404;
         proxy_pass http://monitoring.milevsky.quest:9090/;
         proxy_set_header Host $http_host;
         proxy_set_header X-Forwarded-Host $http_host;
         proxy_set_header X-Real-IP $remote_addr;
         proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
#         proxy_set_header X-Forwarded-Proto https;
#         proxy_set_header HTTPS YES;
    }
}
server {
    listen 443 ssl;
    server_name alertmanager.milevsky.quest;

    ssl on;
    ssl_certificate         /etc/letsencrypt/live/alertmanager.milevsky.quest/fullchain.pem;
    ssl_certificate_key     /etc/letsencrypt/live/alertmanager.milevsky.quest/privkey.pem;
    ssl_trusted_certificate /etc/letsencrypt/live/alertmanager.milevsky.quest/fullchain.pem;

    ssl_session_cache shared:SSL:50m;
    ssl_session_timeout 5m;
    ssl_stapling on;
    ssl_stapling_verify on;

    ssl_protocols TLSv1 TLSv1.1 TLSv1.2;
    ssl_ciphers "ECDHE-RSA-AES256-GCM-SHA384:ECDHE-RSA-AES128-GCM-SHA256:DHE-RSA-AES256-GCM-SHA384:DHE-RSA-AES128-GCM-SHA256:ECDHE-RSA-AES256-SHA384:ECDHE-RSA-AES128-SHA256:ECDHE-RSA-AES256-SHA:ECDHE-RSA-AES128-SHA:DHE-RSA-AES256-SHA256:DHE-RSA-AES128-SHA256:DHE-RSA-AES256-SHA:DHE-RSA-AES128-SHA:ECDHE-RSA-DES-CBC3-SHA:EDH-RSA-DES-CBC3-SHA:AES256-GCM-SHA384:AES128-GCM-SHA256:AES256-SHA256:AES128-SHA256:AES256-SHA:AES128-SHA:DES-CBC3-SHA:HIGH:!aNULL:!eNULL:!EXPORT:!DES:!MD5:!PSK:!RC4";

    ssl_dhparam /etc/nginx/dhparams.pem;
    ssl_prefer_server_ciphers on;
    location / {
#        try_files $uri $uri/ =404;
         proxy_pass http://monitoring.milevsky.quest:9093/;
         proxy_set_header Host $http_host;
         proxy_set_header X-Forwarded-Host $http_host;
         proxy_set_header X-Real-IP $remote_addr;
         proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
#         proxy_set_header X-Forwarded-Proto https;
#         proxy_set_header HTTPS YES;
    }
}
server {
    listen 443 ssl;
    server_name gitlab.milevsky.quest;

    ssl on;
    ssl_certificate         /etc/letsencrypt/live/gitlab.milevsky.quest/fullchain.pem;
    ssl_certificate_key     /etc/letsencrypt/live/gitlab.milevsky.quest/privkey.pem;
    ssl_trusted_certificate /etc/letsencrypt/live/gitlab.milevsky.quest/fullchain.pem;

    ssl_session_cache shared:SSL:50m;
    ssl_session_timeout 5m;
    ssl_stapling on;
    ssl_stapling_verify on;

    ssl_protocols TLSv1 TLSv1.1 TLSv1.2;
    ssl_ciphers "ECDHE-RSA-AES256-GCM-SHA384:ECDHE-RSA-AES128-GCM-SHA256:DHE-RSA-AES256-GCM-SHA384:DHE-RSA-AES128-GCM-SHA256:ECDHE-RSA-AES256-SHA384:ECDHE-RSA-AES128-SHA256:ECDHE-RSA-AES256-SHA:ECDHE-RSA-AES128-SHA:DHE-RSA-AES256-SHA256:DHE-RSA-AES128-SHA256:DHE-RSA-AES256-SHA:DHE-RSA-AES128-SHA:ECDHE-RSA-DES-CBC3-SHA:EDH-RSA-DES-CBC3-SHA:AES256-GCM-SHA384:AES128-GCM-SHA256:AES256-SHA256:AES128-SHA256:AES256-SHA:AES128-SHA:DES-CBC3-SHA:HIGH:!aNULL:!eNULL:!EXPORT:!DES:!MD5:!PSK:!RC4";

    ssl_dhparam /etc/nginx/dhparams.pem;
    ssl_prefer_server_ciphers on;
    location / {
#        try_files $uri $uri/ =404;
         proxy_pass http://gitlab.milevsky.quest;
         proxy_set_header Host $http_host;
         proxy_set_header X-Forwarded-Host $http_host;
         proxy_set_header X-Real-IP $remote_addr;
         proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
#         proxy_set_header X-Forwarded-Proto https;
#         proxy_set_header HTTPS YES;
    }
}
```

[ПОЛУЧЕНИЕ СЕРТИФИКАТА LET’S ENCRYPT С ПОМОЩЬЮ ANSIBLE В UBUNTU 18.04](https://www.8host.com/blog/poluchenie-sertifikata-lets-encrypt-s-pomoshhyu-ansible-v-ubuntu-18-04/)  
nginx let's encrypt для домена
https://www.digitalocean.com/community/tutorials/how-to-secure-nginx-with-let-s-encrypt-on-ubuntu-20-04-ru
https://help.reg.ru/hc/ru/articles/4408047570961-%D0%9A%D0%B0%D0%BA-%D0%B7%D0%B0%D1%89%D0%B8%D1%82%D0%B8%D1%82%D1%8C-Nginx-%D1%81-%D0%BF%D0%BE%D0%BC%D0%BE%D1%89%D1%8C%D1%8E-Let-s-Encrypt-%D0%BD%D0%B0-Ubuntu-18-04-20-04




