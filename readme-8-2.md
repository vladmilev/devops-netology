# Домашнее задание к занятию "08.02 Работа с Playbook"

## Подготовка к выполнению  
1. Cоздайте свой собственный (или используйте старый) публичный репозиторий на github с произвольным именем.
2. Скачайте [playbook](https://github.com/netology-code/mnt-homeworks/blob/master/08-ansible-02-playbook/playbook) из репозитория с домашним заданием и перенесите его в свой репозиторий.
```
использую репозиторий с первого задания https://github.com/vladmilev/ansible каталог под эту домашку ~/netology/ansible02 (машина ansible)
копирую из целевого репозитория (скачан уже в первом задании) в директорию с домашкой:
cp -R ~/netology/mnt-homeworks/08-ansible-02-playbook/playbook/group_vars ~/netology/ansible02
cp -R ~/netology/mnt-homeworks/08-ansible-02-playbook/playbook/inventory ~/netology/ansible02
cp -R ~/netology/mnt-homeworks/08-ansible-02-playbook/playbook/templates ~/netology/ansible02
cp -R ~/netology/mnt-homeworks/08-ansible-02-playbook/playbook/*.* ~/netology/ansible02
```
3. Подготовьте хосты в соответствии с группами из предподготовленного playbook.
```
docker установлен, буду через него сервисы запускать
```
4. Скачайте дистрибутив [java](https://www.oracle.com/java/technologies/javase-jdk11-downloads.html) и положите его в директорию playbook/files/.
```
$ mkdir files
$ cd files
$ curl -O https://www.oracle.com/webapps/redirect/signon?nexturl=https://download.oracle.com/otn/java/jdk/11.0.15.1%2B2/d76aabb62f1c47aa8588b9ae5a8a5b46/jdk-11.0.15.1_linux-x64_bin.tar.gz
```

## Основная часть
1. Приготовьте свой собственный inventory файл prod.yml.
```

```
2. Допишите playbook: нужно сделать ещё один play, который устанавливает и настраивает kibana.
```

```
3. При создании tasks рекомендую использовать модули: get_url, template, unarchive, file.
```

```
4. Tasks должны: скачать нужной версии дистрибутив, выполнить распаковку в выбранную директорию, сгенерировать конфигурацию с параметрами.
```

```
5. Запустите ansible-lint site.yml и исправьте ошибки, если они есть.
```

```
6. Попробуйте запустить playbook на этом окружении с флагом --check.
```

```
7. Запустите playbook на prod.yml окружении с флагом --diff. Убедитесь, что изменения на системе произведены.
```

```
8. Повторно запустите playbook с флагом --diff и убедитесь, что playbook идемпотентен.
```

```
9. Подготовьте README.md файл по своему playbook. В нём должно быть описано: что делает playbook, какие у него есть параметры и теги.
```

```
10. Готовый playbook выложите в свой репозиторий, в ответ предоставьте ссылку на него.
```

```

## Необязательная часть
1. Приготовьте дополнительный хост для установки logstash.
2. Пропишите данный хост в prod.yml в новую группу logstash.
3. Дополните playbook ещё одним play, который будет исполнять установку logstash только на выделенный для него хост.
4. Все переменные для нового play определите в отдельный файл group_vars/logstash/vars.yml.
5. Logstash конфиг должен конфигурироваться в части ссылки на elasticsearch (можно взять, например его IP из facts или определить через vars).

Дополните README.md, протестируйте playbook, выложите новую версию в github. В ответ предоставьте ссылку на репозиторий.
