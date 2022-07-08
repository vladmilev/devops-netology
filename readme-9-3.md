# Домашнее задание к занятию "09.03 Jenkins"

## Подготовка к выполнению
1. Установить jenkins по любой из [инструкций](https://www.jenkins.io/download/)
```
установил для ВМ c ubuntu https://pkg.jenkins.io/debian-stable/
ВМ (may1) с открытым для сервера jenkins портом 8080

попытка выполнить 1-ю строку по инструкции - выдает ошибку

vagrant@ubuntu-bionic:~$   curl -fsSL https://pkg.jenkins.io/debian-stable/jenkins.io.key | sudo tee \
>     /usr/share/keyrings/jenkins-keyring.asc > /dev/null
curl: (6) Could not resolve host: pkg.jenkins.io

С другой инструкцией https://www.digitalocean.com/community/tutorials/how-to-install-jenkins-on-ubuntu-20-04-ru
другая ошибка

vagrant@ubuntu-bionic:~$ wget -q -O - https://pkg.jenkins.io/debian-stable/jenkins.io.key | sudo apt-key add -
gpg: no valid OpenPGP data found.

Что делать и как быть?
```
2. Запустить и проверить работоспособность
```

```
3. Сделать первоначальную настройку
```

```
4. Настроить под свои нужды
```

```
5. Поднять отдельный cloud
```

```
6. Для динамических агентов можно использовать образ
```

```
7. Обязательный параметр: поставить label для динамических агентов: ansible_docker
```

```
8. Сделать форк репозитория с playbook
```

```

## Основная часть   
1. Сделать Freestyle Job, который будет запускать ansible-playbook из форка репозитория
2. Сделать Declarative Pipeline, который будет выкачивать репозиторий с плейбукой и запускать её
3. Перенести Declarative Pipeline в репозиторий в файл Jenkinsfile
4. Перенастроить Job на использование Jenkinsfile из репозитория
5. Создать Scripted Pipeline, наполнить его скриптом из pipeline
6. Заменить credentialsId на свой собственный
7. Проверить работоспособность, исправить ошибки, исправленный Pipeline вложить в репозитрий в файл ScriptedJenkinsfile
8. Отправить ссылку на репозиторий в ответе

## Необязательная часть
1. Создать скрипт на groovy, который будет собирать все Job, которые завершились хотя бы раз неуспешно. Добавить скрипт в репозиторий с решеним с названием AllJobFailure.groovy
2. Установить customtools plugin
3. Поднять инстанс с локальным nexus, выложить туда в анонимный доступ .tar.gz с ansible версии 2.9.x
4. Создать джобу, которая будет использовать ansible из customtool
5. Джоба должна просто исполнять команду ansible --version, в ответ прислать лог исполнения джобы
