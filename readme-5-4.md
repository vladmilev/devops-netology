# Домашнее задание к занятию "5.4. Оркестрация группой Docker контейнеров на примере Docker Compose"


## Задача 1
Создать собственный образ операционной системы с помощью Packer.  
Для получения зачета, вам необходимо предоставить: cкриншот страницы, как на слайде из презентации (слайд 37).
```
Авторизовался на Яндекс.Облако, создал облако cloud-vladmilev

Установка Packer (https://www.packer.io/downloads)
$ curl -fsSL https://apt.releases.hashicorp.com/gpg | sudo apt-key add -
$ sudo apt-add-repository "deb [arch=amd64] https://apt.releases.hashicorp.com $(lsb_release -cs) main"
$ sudo apt-get update && sudo apt-get install packer
~$ sudo snap install packer
2022-01-31T19:14:11Z INFO Waiting for automatic snapd restart...
packer 1.0.0-2 from Nathan Handler (nhandler) installed
vagrant@ubuntu-bionic:~$ packer --version
1.0.0

Установка Terraform (terraform.io)
$ sudo apt-get update && sudo apt-get install -y gnupg software-properties-common curl
$ curl -fsSL https://apt.releases.hashicorp.com/gpg | sudo apt-key add -
$ sudo apt-add-repository "deb [arch=amd64] https://apt.releases.hashicorp.com $(lsb_release -cs) main"
$ sudo apt-get update && sudo apt-get install terraform
$ $ terraform --version
Terraform v1.1.4
on linux_amd64

Установка утилиты yc


```

## Задача 2
Создать вашу первую виртуальную машину в Яндекс.Облаке.  
Для получения зачета, вам необходимо предоставить:  cкриншот страницы свойств созданной ВМ.
```

```

## Задача 3
Создать ваш первый готовый к боевой эксплуатации компонент мониторинга, состоящий из стека микросервисов.  
Для получения зачета, вам необходимо предоставить: cкриншот работающего веб-интерфейса Grafana с текущими метриками.
```

```

Задача 4 (*)
Создать вторую ВМ и подключить её к мониторингу развёрнутому на первом сервере.  
Для получения зачета, вам необходимо предоставить: cкриншот из Grafana, на котором будут отображаться метрики добавленного вами сервера.
```

```
