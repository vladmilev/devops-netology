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
остановил контейнеры с предыдущего задания
2# docker stop 809de5580da8 e1b451bfc80f
809de5580da8
e1b451bfc80f
root@ubuntu-bionic:~/netology/ansible02# docker rm 809de5580da8 e1b451bfc80f
запустил их как elastic-1 и kibana-1 на centos7 (есть закачанный образ) 
# docker stop 809de5580da8 e1b451bfc80f
root@ubuntu-bionic:~/netology/ansible02# docker rm 809de5580da8 e1b451bfc80f
root@ubuntu-bionic:~/netology/ansible02# docker image list
REPOSITORY                                         TAG       IMAGE ID       CREATED         SIZE
registry.gitlab.com/vlad_milev/mnt-ci/python-api   latest    c60f9e6a5a0a   3 weeks ago     432MB
ghcr.io/runatlantis/atlantis                       latest    af9576465a67   5 weeks ago     592MB
sonatype/nexus3                                    latest    b7c023b6a9b9   2 months ago    655MB
pycontribs/centos                                  7         bafa54e44377   14 months ago   488MB
pycontribs/ubuntu                                  latest    42a4e3b21923   2 years ago     664MB
root@ubuntu-bionic:~/netology/ansible02# docker run -d --name elastic-1 bafa54e44377 /bin/sleep 100000000000000
f579c89dd2ccbb4a7cc676bd95d35b9dbdaa9e685e9867c5c85a9e518f98a59f
root@ubuntu-bionic:~/netology/ansible02# docker run -d --name kibana-1 bafa54e44377 /bin/sleep 100000000000000
36873ceec68a34900847ee23c98602de5fea0035559b4ba66b9f8ab66a1524f3
root@ubuntu-bionic:~/netology/ansible02# docker ps
CONTAINER ID   IMAGE          COMMAND                  CREATED          STATUS          PORTS     NAMES
36873ceec68a   bafa54e44377   "/bin/sleep 10000000…"   17 seconds ago   Up 16 seconds             kibana-1
f579c89dd2cc   bafa54e44377   "/bin/sleep 10000000…"   44 seconds ago   Up 43 seconds             elastic-1
```
4. Скачайте дистрибутив [java](https://www.oracle.com/java/technologies/javase-jdk11-downloads.html) и положите его в директорию playbook/files/.
```
$ mkdir files
$ cd files
Скачал через VPN (иначе блокирует - 403) на виндос-машину, скопировал в папку вагранта, затем на вирт.машине нашел этот архив и скопировал в целевую папку
root@ubuntu-bionic:/vagrant# cp jdk-11.0.15.1_linux-x64_bin.tar.gz ~/netology/ansible02/files/jdk-11.0.15.1_linux-x64_bin.tar.gz
root@ubuntu-bionic:~/netology/ansible02/files# ll
-rwxr-xr-x 1 vagrant vagrant 168829286 Jun 24 03:01 jdk-11.0.15.1_linux-x64_bin.tar.gz*
```

## Основная часть
1. Приготовьте свой собственный inventory файл prod.yml.
```
vagrant@ubuntu-bionic:~/netology/ansible02/inventory$ nano prod.yml
---
elasticsearch:
  hosts:
    elastic-1:
      ansible_connection: docker
kibana:
  hosts:
    kibana-1:
      ansible_connection: docker
```
2. Допишите playbook: нужно сделать ещё один play, который устанавливает и настраивает kibana.
```
kibana - пример установки прикладной программы, по-сути, будет дублировать заданные таски по установке elasticsearch
```
3. При создании tasks рекомендую использовать модули: get_url, template, unarchive, file.
```
get_url - будет использован для скачивания архива программы
file - для создания директории 
unarchive - разархивирование архива
template - копирование требуемой конфигурации
```
4. Tasks должны: скачать нужной версии дистрибутив, выполнить распаковку в выбранную директорию, сгенерировать конфигурацию с параметрами.
```
- name: Install Kibana
  hosts: kibana
  tasks:
    - name: Upload tar.gz kibana from remote URL
      get_url:
        url: "https://artifacts.elastic.co/downloads/kibana/kibana-{{ kibana_version }}-linux-x86_64.tar.gz"
        dest: "/tmp/kibana-{{ kibana_version }}-linux-x86_64.tar.gz"
        mode: 0755
        timeout: 60
        force: true
        validate_certs: false
      register: get_kibana
      until: get_kibana is succeeded
      tags: kibana
    - name: Create directrory for Kibana
      file:
        state: directory
        path: "{{ kibana_home }}"
      tags: kibana
    - name: Extract Kibana in the installation directory
      become: true
      unarchive:
        copy: false
        src: "/tmp/kibana-{{ kibana_version }}-linux-x86_64.tar.gz"
        dest: "{{ kibana_home }}"
        extra_opts: [--strip-components=1]
        creates: "{{ kibana_home }}/bin/kibana"
      tags:
        - skip_ansible_lint
        - kibana
    - name: Set environment Kibana
      become: true
      template:
        src: templates/kib.sh.j2
        dest: /etc/profile.d/kib.sh
      tags: kibana
      
Дополнительно добавлена конфигурация template\kib.sh.j2
# Warning: This file is Ansible Managed, manual changes will be overwritten on next playbook run.
#!/usr/bin/env bash
export KIBANA_HOME={{ kibana_home }}
export PATH=$PATH:$KIBANA_HOME/bin

переменные group_vars/kibana/vars.yml
---
  kibana_version: "7.12.0"
  kibana_home: "/opt/kibana/{{ kibana_version }}"
```
5. Запустите ansible-lint site.yml и исправьте ошибки, если они есть.
```
$ sudo pip3 install ansible-lint
risky-file-permissions: File permissions unset or incorrect
похоже на варнинг
```
6. Попробуйте запустить playbook на этом окружении с флагом --check.
```
$ sudo ansible-playbook -i inventory/prod.yml site.yml --check
fatal: [elastic-1]: FAILED! => {"attempts": 3, "changed": false, "dest": "/tmp/elasticsearch-7.10.1-linux-x86_64.tar.gz", "elapsed": 0, "msg": "Request failed", "response": "HTTP Error 403: Forbidden", "status_code": 403, "url": "https://artifacts.elastic.co/downloads/elasticsearch/elasticsearch-7.10.1-linux-x86_64.tar.gz"}

Похоже скачивание архива из России заблокировано, нужно зеркало - скачал архивы через VPN и забросил на доступный хостинг - указав для скачивания ссылки в site.yml

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
# git add ansible02/*
# git status
# git commit -m "homework 8.2"
$ git push

Ссылка на репозиторий: https://github.com/vladmilev/ansible/tree/main/ansible02
```

## Необязательная часть
1. Приготовьте дополнительный хост для установки logstash.
2. Пропишите данный хост в prod.yml в новую группу logstash.
3. Дополните playbook ещё одним play, который будет исполнять установку logstash только на выделенный для него хост.
4. Все переменные для нового play определите в отдельный файл group_vars/logstash/vars.yml.
5. Logstash конфиг должен конфигурироваться в части ссылки на elasticsearch (можно взять, например его IP из facts или определить через vars).

Дополните README.md, протестируйте playbook, выложите новую версию в github. В ответ предоставьте ссылку на репозиторий.
