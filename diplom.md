# Дипломный практикум в YandexCloud

## Доменное имя
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

## Облачная инфраструктура 
(развертывание инфраструктуры с помощью Terraform на базе облачного провайдера YandexCloud)

На виртуальной машине (may1) уже оказались установлены yc и terraform  
Настройку бакета (бекэнда) делал по [Загрузка состояний Terraform в Object Storage](https://cloud.yandex.ru/docs/tutorials/infrastructure-management/terraform-state-storage)  

предварительно - создал сервис-аккаунт
$ yc iam service

nginx let's encrypt для домена
https://www.digitalocean.com/community/tutorials/how-to-secure-nginx-with-let-s-encrypt-on-ubuntu-20-04-ru
https://help.reg.ru/hc/ru/articles/4408047570961-%D0%9A%D0%B0%D0%BA-%D0%B7%D0%B0%D1%89%D0%B8%D1%82%D0%B8%D1%82%D1%8C-Nginx-%D1%81-%D0%BF%D0%BE%D0%BC%D0%BE%D1%89%D1%8C%D1%8E-Let-s-Encrypt-%D0%BD%D0%B0-Ubuntu-18-04-20-04


