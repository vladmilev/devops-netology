# Домашнее задание к занятию "08.03 Работа с Roles"

## Подготовка к выполнению  
1. Создайте два пустых публичных репозитория в любом своём проекте: elastic-role и kibana-role.
```
Сначала создал 2 public-репозитория на github.com (elastic-role и kibana-role)
Инициализирую новый репозиторий https://github.com/vladmilev/elastic-role
$ mkdir elastic-role
$ cd elastic-role
echo "# elastic-role" >> README.md
git init
git add README.md
git commit -m "first commit"
git branch -M main
git remote add origin git@github.com:vladmilev/elastic-role.git
git push -u origin main

Инициализирую новый репозиторий https://github.com/vladmilev/kibana-role
$ mkdir kibana-role
$ cd kibana-role
echo "# kibana-role" >> README.md
git init
git add README.md
git commit -m "first commit"
git branch -M main
git remote add origin git@github.com:vladmilev/kibana-role.git
git push -u origin main
```
2. Скачайте [role](https://github.com/netology-code/mnt-homeworks/blob/master/08-ansible-03-role/roles) из репозитория с домашним заданием и перенесите его в свой репозиторий elastic-role.
```
Копирую файлы ДЗ в поддиректрию elastic-role
vagrant@ubuntu-bionic:~/elastic-role$ cp -R ~/netology/mnt-homeworks/08-ansible-03-role/roles/* ~/elastic-role
vagrant@ubuntu-bionic:~/elastic-role$ cp -R ~/netology/mnt-homeworks/08-ansible-03-role/roles/*.* ~/elastic-role  
vagrant@ubuntu-bionic:~/elastic-role$ cp -R ~/netology/mnt-homeworks/08-ansible-03-role/roles/.travis.yml ~/elastic-role
vagrant@ubuntu-bionic:~/elastic-role$ ll
total 60
drwxrwxr-x 11 vagrant vagrant 4096 Jun 27 06:38 ./
drwxr-xr-x 18 vagrant vagrant 4096 Jun 27 06:31 ../
drwxrwxr-x  8 vagrant vagrant 4096 Jun 27 06:36 .git/
-rw-rw-r--  1 vagrant vagrant  539 Jun 27 06:39 .travis.yml
-rw-rw-r--  1 vagrant vagrant  598 Jun 27 06:38 .yamllint
-rw-rw-r--  1 vagrant vagrant 4436 Jun 27 06:38 README.md
drwxrwxr-x  2 vagrant vagrant 4096 Jun 27 06:36 defaults/
drwxrwxr-x  2 vagrant vagrant 4096 Jun 27 06:36 handlers/
drwxrwxr-x  2 vagrant vagrant 4096 Jun 27 06:36 meta/
drwxrwxr-x  3 vagrant vagrant 4096 Jun 27 06:36 molecule/
drwxrwxr-x  2 vagrant vagrant 4096 Jun 27 06:36 tasks/
drwxrwxr-x  2 vagrant vagrant 4096 Jun 27 06:36 templates/
drwxrwxr-x  2 vagrant vagrant 4096 Jun 27 06:36 tests/
drwxrwxr-x  2 vagrant vagrant 4096 Jun 27 06:36 vars/
```
3. Скачайте дистрибутив [java](https://www.oracle.com/java/technologies/javase-jdk11-downloads.html) и положите его в директорию `playbook/files/`.
```
Скачивал ранее для ДЗ-8-2 
(через VPN на виндос-машину, копировал в папку вагранта, затем на вирт.машине нашел этот архив и скопировал в целевую папку)
vagrant@ubuntu-bionic:~/netology/ansible03$ mkdir files
vagrant@ubuntu-bionic:/vagrant# cp jdk-11.0.15.1_linux-x64_bin.tar.gz ~/netology/ansible03/files/jdk-11.0.15.1_linux-x64_bin.tar.gz
vagrant@ubuntu-bionic:~$ cd ~/netology/ansible03/files/
vagrant@ubuntu-bionic:~/netology/ansible03/files$ ll
\total 164888
drwxrwxr-x 2 vagrant vagrant      4096 Jun 27 06:45 ./
drwxrwxr-x 3 vagrant vagrant      4096 Jun 27 06:42 ../
-rwxrwxr-x 1 vagrant vagrant 168829286 Jun 27 06:45 jdk-11.0.15.1_linux-x64_bin.tar.gz*
```
4. Установите molecule: `pip3 install molecule`
```
выдавала множество ошибок
обновил pip
$ sudo apt-get install python3-pip
$ sudo pip3 install --upgrade pip
установил с флагом --ignore-installed под root
root@ubuntu-bionic:~/netology/ansible03# pip3 install molecule --ignore-installed
```
5. Добавьте публичную часть своего ключа к своему профилю в github.
```
ранее, пушил по SSH в github репозитории elastic-role и kibana-role - значит ключ рабочий и публичная часть опубликована на github
```

## Основная часть  
Наша основная цель - разбить наш playbook на отдельные roles.  
Задача: сделать roles для elastic, kibana и написать playbook для использования этих ролей.  
Ожидаемый результат: существуют два ваших репозитория с roles и один репозиторий с playbook.

1. Создать в старой версии playbook файл requirements.yml и заполнить его следующим содержимым:  
```
---
  - src: git@github.com:netology-code/mnt-homeworks-ansible.git
    scm: git
    version: "1.0.1"
    name: java 
```
2. При помощи ansible-galaxy скачать себе эту роль. Запустите molecule test, посмотрите на вывод команды.
```
Установил только после выхода из рута
vagrant@ubuntu-bionic:~/netology/ansible03$ ansible-galaxy install -r requirements.yml -p roles
[DEPRECATION WARNING]: Ansible will require Python 3.8 or newer on the controller starting with Ansible 2.12.
 Current version: 3.6.9 (default, Mar 15 2022, 13:55:28) [GCC 8.4.0]. This feature will be removed from
ansible-core in version 2.12. Deprecation warnings can be disabled by setting deprecation_warnings=False in
ansible.cfg.
/usr/local/lib/python3.6/dist-packages/ansible/parsing/vault/__init__.py:44: CryptographyDeprecationWarning: Python 3.6 is no longer supported by the Python core team. Therefore, support for it is deprecated in cryptography and will be removed in a future release.
  from cryptography.exceptions import InvalidSignature
Starting galaxy role install process
- extracting java to /home/vagrant/netology/ansible03/roles/java
- extracting java to /home/vagrant/.ansible/roles/java
- java (1.0.1) was installed successfully

Но в указанной папке не было java - изменил владельца на папку и файл
$ sudo chown vagrant:vagrant roles
$ sudo chown vagrant:vagrant requirements.yml
Так - появилась папка с ролью java в каталоге roles

$ molecule test
Ошибка 
CRITICAL Failed to pre-validate .. unallowed value docker
$ pip3 install molecule-docker
vagrant@ubuntu-bionic:~/netology/ansible03/roles/java$ molecule test
ERROR    Computed fully qualified role name of java does not follow current galaxy requirements.
Please edit meta/main.yml and assure we can correctly determine full role name
- теперь прошло
```
3. Перейдите в каталог с ролью elastic-role и создайте сценарий тестирования по умолчаню при помощи molecule init scenario --driver-name docker.
```
пришлось удалить папку elastic-role\molecula\default
vagrant@ubuntu-bionic:~/elastic-role$ molecule init scenario --driver-name docker
INFO     Initializing new scenario default...
INFO     Initialized scenario in /home/vagrant/elastic-role/molecule/default successfully.
```
4. Добавьте несколько разных дистрибутивов (centos:8, ubuntu:latest) для инстансов и протестируйте роль, исправьте найденные ошибки, если они есть.
```
- инстансы для тестирования через molecule указываются в сценарии
$ cat  molecule.yml
---
dependency:
  name: galaxy
driver:
  name: docker
platforms:
  - name: instance
    image: quay.io/centos/centos:stream8
    pre_build_image: true
provisioner:
  name: ansible
verifier:
  name: ansible

Изменил - добавив еще ubuntu:latest, centos7, centos8
$ nano molecule.yml
---
dependency:
  name: galaxy
driver:
  name: docker
platforms:
  - name: centos8
    image: docker.io/pycontribs/centos:8
    pre_build_image: true
  - name: centos7
    image: docker.io/pycontribs/centos:7
    pre_build_image: true
  - name: ubuntu
    image: docker.io/pycontribs/ubuntu:latest
    pre_build_image: true
provisioner:
  name: ansible
verifier:
  name: ansible

$ molecule test
ERROR    Computed fully qualified role name of elastic-role does not follow current galaxy requirements.
Please edit meta/main.yml and assure we can correctly determine full role name:
galaxy_info:
role_name: my_name  # if absent directory name hosting role is used instead
namespace: my_galaxy_namespace  # if absent, author is used instead

- Добавил в meta/main.yml блок
galaxy_info:
  role_name: linux_administration
  namespace: vagrant
  
$ sudo molecule test

fatal: [centos7]: FAILED! => {"attempts": 3, "changed": false, "dest": "/tmp/elasticsearch-7.10.1-linux-x86_64.tar.gz", "elapsed": 0, "msg": "Request failed", "response": "HTTP Error 403: Forbidden", "status_code": 403, "url": "https://artifacts.elastic.co/downloads/elasticsearch/elasticsearch-7.10.1-linux-x86_64.tar.gz"}
fatal: [ubuntu]: FAILED! => {"attempts": 3, "changed": false, "dest": "/tmp/elasticsearch-7.10.1-linux-x86_64.tar.gz", "elapsed": 0, "msg": "Request failed", "response": "HTTP Error 403: Forbidden", "status_code": 403, "url": "https://artifacts.elastic.co/downloads/elasticsearch/elasticsearch-7.10.1-linux-x86_64.tar.gz"}
fatal: [centos8]: FAILED! => {"attempts": 3, "changed": false, "dest": "/tmp/elasticsearch-7.10.1-linux-x86_64.tar.gz", "elapsed": 0, "msg": "Request failed", "response": "HTTP Error 403: Forbidden", "status_code": 403, "url": "https://artifacts.elastic.co/downloads/elasticsearch/elasticsearch-7.10.1-linux-x86_64.tar.gz"}

PLAY RECAP *********************************************************************
centos7                    : ok=1    changed=0    unreachable=0    failed=1    skipped=0    rescued=0    ignored=0
centos8                    : ok=1    changed=0    unreachable=0    failed=1    skipped=0    rescued=0    ignored=0
ubuntu                     : ok=1    changed=0    unreachable=0    failed=1    skipped=0    rescued=0    ignored=0

403 - заблокированы по IP при попытке скачать, но процесс был запущен!)
- заменил в task на скачивание (get_url) url на свое зеркало url: "https://domstor.ru/userfiles/realt-translator/test/kibana-{{ kibana_version }}-linux-x86_64.tar.gz"
выполнило:
PLAY RECAP *********************************************************************
localhost                  : ok=2    changed=2    unreachable=0    failed=0    skipped=1    rescued=0    ignored=0
INFO     Pruning extra files from scenario ephemeral directory
```
5. Создайте новый каталог с ролью при помощи molecule init role --driver-name docker kibana-role. Можете использовать другой драйвер, который более удобен вам.
```
vagrant@ubuntu-bionic:~/netology/ansible03$ molecule init role --driver-name docker kibana-role

CRITICAL Outside collections you must mention role namespace like: molecule init role 'acme.myrole'. Be sure you use only lowercase characters and underlines. See https://galaxy.ansible.com/docs/contributing/creating_role.html
```
6. На основе tasks из старого playbook заполните новую role. Разнесите переменные между vars и default. Проведите тестирование на разных дистрибитивах (centos:7, centos:8, ubuntu).
```
vagrant@ubuntu-bionic:~$ ansible-galaxy init kibana-role --force
- Role kibana-role was created successfully
vagrant@ubuntu-bionic:~$ cd kibana-role
vagrant@ubuntu-bionic:~/kibana-role$ ll
total 52
drwxrwxr-x 11 vagrant vagrant 4096 Jun 27 09:58 ./
drwxr-xr-x 20 vagrant vagrant 4096 Jun 27 09:00 ../
drwxrwxr-x  8 vagrant vagrant 4096 Jun 27 06:33 .git/
-rw-rw-r--  1 vagrant vagrant  539 Jun 27 09:58 .travis.yml
-rw-rw-r--  1 vagrant vagrant 1328 Jun 27 09:58 README.md
drwxrwxr-x  2 vagrant vagrant 4096 Jun 27 09:58 defaults/
drwxrwxr-x  2 vagrant vagrant 4096 Jun 27 09:58 files/
drwxrwxr-x  2 vagrant vagrant 4096 Jun 27 09:58 handlers/
drwxrwxr-x  2 vagrant vagrant 4096 Jun 27 09:58 meta/
drwxrwxr-x  2 vagrant vagrant 4096 Jun 27 09:58 tasks/
drwxrwxr-x  2 vagrant vagrant 4096 Jun 27 09:58 templates/
drwxrwxr-x  2 vagrant vagrant 4096 Jun 27 09:58 tests/
drwxrwxr-x  2 vagrant vagrant 4096 Jun 27 09:58 vars/

Переменные записал в defaults
Создал сценарий тестирования molecule init scenario --driver-name docker
- добавил в него инстансы ubuntu:latest, centos7, centos8
vagrant@ubuntu-bionic:~/kibana-role$ nano molecule/default/molecule.yml
---
dependency:
  name: galaxy
driver:
  name: docker
platforms:
  - name: centos8
    image: docker.io/pycontribs/centos:8
    pre_build_image: true
  - name: centos7
    image: docker.io/pycontribs/centos:7
    pre_build_image: true
  - name: ubuntu
    image: docker.io/pycontribs/ubuntu:latest
    pre_build_image: true
provisioner:
  name: ansible
verifier:
  name: ansible

Провел тестирование
$ sudo molecule test
PLAY RECAP *********************************************************************
centos7                    : ok=4    changed=0    unreachable=0    failed=0    skipped=1    rescued=0    ignored=0
centos8                    : ok=4    changed=0    unreachable=0    failed=0    skipped=1    rescued=0    ignored=0
ubuntu                     : ok=4    changed=0    unreachable=0    failed=0    skipped=1    rescued=0    ignored=0
```
7. Выложите все roles в репозитории. Проставьте тэги, используя семантическую нумерацию.
```
https://github.com/vladmilev/elastic-role
https://github.com/vladmilev/kibana-role
```
8. Добавьте roles в requirements.yml в playbook.
```

```
9. Переработайте playbook на использование roles.
```

```
10. Выложите playbook в репозиторий.
```

```
11. В ответ приведите ссылки на оба репозитория с roles и одну ссылку на репозиторий с playbook.
```

```

## Необязательная часть  
Проделайте схожие манипуляции для создания роли logstash.  
Создайте дополнительный набор tasks, который позволяет обновлять стек ELK.  
В ролях добавьте тестирование в раздел verify.yml. Данный раздел должен проверять,  
что elastic запущен и возвращает успешный статус по API, web-интерфейс kibana отвечает без кодов ошибки,  
logstash через команду logstash -e 'input { stdin { } } output { stdout {} }'.  
Убедитесь в работоспособности своего стека. Возможно, потребуется тестировать все роли одновременно.  
Выложите свои roles в репозитории. В ответ приведите ссылки.  
