# Домашнее задание к занятию "08.01 Введение в Ansible"

## Подготовка к выполнению  
- Установите ansible версии 2.10 или выше.  
- Создайте свой собственный публичный репозиторий на github с произвольным именем.  
- Скачайте playbook из репозитория с домашним заданием и перенесите его в свой репозиторий.  
```
создал новый репозиторий под это задание - ansible, каталог под домашку ~/netology/ansible01
работал через vagrant (машина ansible)
установил docker
# sudo apt install docker.io
docker.io is already the newest version (20.10.7-0ubuntu5~18.04.3).
установил ansible
# sudo apt install python3-pip -y
# sudo pip3 install ansible

$ ansible --version
[DEPRECATION WARNING]: Ansible will require Python 3.8 or newer on the
controller starting with Ansible 2.12. Current version: 3.6.9 (default, Mar 15
2022, 13:55:28) [GCC 8.4.0]. This feature will be removed from ansible-core in
version 2.12. Deprecation warnings can be disabled by setting
deprecation_warnings=False in ansible.cfg.
ansible [core 2.11.12]
  config file = None
  configured module search path = ['/home/vagrant/.ansible/plugins/modules', '/usr/share/ansible/plugins/modules']
  ansible python module location = /usr/local/lib/python3.6/dist-packages/ansible
  ansible collection location = /home/vagrant/.ansible/collections:/usr/share/ansible/collections
  executable location = /usr/local/bin/ansible
  python version = 3.6.9 (default, Mar 15 2022, 13:55:28) [GCC 8.4.0]
  jinja version = 2.10
  libyaml = True
  
Копирую исходный репозиторий 
$ mkdir netology
$ cd netology
~/netology$ git clone git@github.com:vladmilev/mnt-homeworks.git

Инициализирую новый репозиторий https://github.com/vladmilev/ansible
echo "# ansible" >> README.md
git init
git add README.md
git commit -m "first commit"
git branch -M main
git remote add origin git@github.com:vladmilev/ansible.git
git push -u origin main

Копирую файлы ДЗ в поддиректрию ansible-01
vagrant@ubuntu-bionic:~/netology/ansible01$ cp -R ~/netology/mnt-homeworks/08-ansible-01-base/playbook/group_vars ~/netology/ansible01
vagrant@ubuntu-bionic:~/netology/ansible01$ cp -R ~/netology/mnt-homeworks/08-ansible-01-base/playbook/inventory ~/netology/ansible01
vagrant@ubuntu-bionic:~/netology/ansible01$ cp -R ~/netology/mnt-homeworks/08-ansible-01-base/playbook/*.* ~/netology/ansible01
vagrant@ubuntu-bionic:~/netology/ansible01$ ll
total 24
drwxrwxr-x 4 vagrant vagrant 4096 Jun 20 03:46 ./
drwxrwxr-x 5 vagrant vagrant 4096 Jun 20 03:35 ../
-rw-rw-r-- 1 vagrant vagrant 1293 Jun 20 03:46 README.md
drwxrwxr-x 5 vagrant vagrant 4096 Jun 20 03:44 group_vars/
drwxrwxr-x 2 vagrant vagrant 4096 Jun 20 03:45 inventory/
-rw-rw-r-- 1 vagrant vagrant  209 Jun 20 03:46 site.yml

git add *
git commit -m "added files with homework-08-01"
git push -u origin main
```

## Основная часть  
1. Попробуйте запустить playbook на окружении из test.yml, зафиксируйте какое значение имеет факт some_fact для указанного хоста при выполнении playbook'a.  
```
$ ansible-playbook -i inventory/test.yml site.yml
запустил задачи из site.yml на хостах из окружения inventory/test.yml (1 хост- localhost)
TASK [Print OS] *************************************************************************************************
ok: [localhost] => {
    "msg": "Ubuntu"
}

TASK [Print fact] ***********************************************************************************************
ok: [localhost] => {
    "msg": 12
}

- переменная some_fact=12
```
2. Найдите файл с переменными (group_vars) в котором задаётся найденное в первом пункте значение и поменяйте его на 'all default fact'.  
```
в файле /group_vars/all/examp.yml
$ nano group_vars/all/examp.yml
  some_fact: "all default fact"
```
3. Воспользуйтесь подготовленным (используется docker) или создайте собственное окружение для проведения дальнейших испытаний.  
```
подчистил контейнеры и образы (docker rm, rmi), скачал нужные образы
# sudo -s
# docker pull pycontribs/centos:7
# docker pull pycontribs/ubuntu:latest

смотрим список образов
# docker image list
REPOSITORY                                         TAG       IMAGE ID       CREATED         SIZE
registry.gitlab.com/vlad_milev/mnt-ci/python-api   latest    c60f9e6a5a0a   2 weeks ago     432MB
ghcr.io/runatlantis/atlantis                       latest    af9576465a67   5 weeks ago     592MB
sonatype/nexus3                                    latest    b7c023b6a9b9   2 months ago    655MB
pycontribs/centos                                  7         bafa54e44377   14 months ago   488MB
pycontribs/ubuntu                                  latest    42a4e3b21923   2 years ago     664MB

запускаем докер-контейнеры с нужными образами (для prod окружения - inventory/prod.yml)
# docker run -d --name centos7 bafa54e44377 /bin/sleep 1000000000000000000
e1b451bfc80f500d7344c20a7899991fd876c33205ca7ae3f39c91947be2b565
# docker run -d --name ubuntu 42a4e3b21923 /bin/sleep 1000000000000000000
809de5580da887efa49386b710f761e6809807a354b2535fc5dcb7bc749fd23a
# docker ps
CONTAINER ID   IMAGE          COMMAND                  CREATED          STATUS          PORTS     NAMES
809de5580da8   42a4e3b21923   "/bin/sleep 10000000…"   5 seconds ago    Up 4 seconds              ubuntu
e1b451bfc80f   bafa54e44377   "/bin/sleep 10000000…"   34 seconds ago   Up 32 seconds             centos7
```
4. Проведите запуск playbook на окружении из prod.yml. Зафиксируйте полученные значения some_fact для каждого из managed host.  
```
# ansible-playbook -i inventory/prod.yml site.yml

PLAY [Print os facts] *************************************************************************************************************************************

TASK [Gathering Facts] ************************************************************************************************************************************
ok: [ubuntu]
ok: [centos7]

TASK [Print OS] *******************************************************************************************************************************************
ok: [centos7] => {
    "msg": "CentOS"
}
ok: [ubuntu] => {
    "msg": "Ubuntu"
}

TASK [Print fact] *****************************************************************************************************************************************
ok: [centos7] => {
    "msg": "el"
}
ok: [ubuntu] => {
    "msg": "deb"
}

PLAY RECAP ************************************************************************************************************************************************
centos7                    : ok=3    changed=0    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0
ubuntu                     : ok=3    changed=0    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0

some_fact - для хоста centos7 ="el", для хоста ubuntu ="deb" 
```
5. Добавьте факты в group_vars каждой из групп хостов так, чтобы для some_fact получились следующие значения: для deb - 'deb default fact', для el - 'el default fact'
```

```
6. Повторите запуск playbook на окружении prod.yml. Убедитесь, что выдаются корректные значения для всех хостов.  
```

```
7. При помощи ansible-vault зашифруйте факты в group_vars/deb и group_vars/el с паролем netology.  
```

```
8. Запустите playbook на окружении prod.yml. При запуске ansible должен запросить у вас пароль. Убедитесь в работоспособности.  
```

```
9. Посмотрите при помощи ansible-doc список плагинов для подключения. Выберите подходящий для работы на control node.  
```

```
10. В prod.yml добавьте новую группу хостов с именем local, в ней разместите localhost с необходимым типом подключения.  
```

```
11. Запустите playbook на окружении prod.yml. При запуске ansible должен запросить у вас пароль. Убедитесь что факты some_fact для каждого из хостов определены из верных group_vars.  
```

```
12. Заполните README.md ответами на вопросы. Сделайте git push в ветку master. В ответе отправьте ссылку на ваш открытый репозиторий с изменённым playbook и заполненным README.md.  
```

```

## Необязательная часть  
При помощи ansible-vault расшифруйте все зашифрованные файлы с переменными.  
Зашифруйте отдельное значение PaSSw0rd для переменной some_fact паролем netology. Добавьте полученное значение в group_vars/all/exmp.yml.  
Запустите playbook, убедитесь, что для нужных хостов применился новый fact.  
Добавьте новую группу хостов fedora, самостоятельно придумайте для неё переменную. В качестве образа можно использовать этот.  
Напишите скрипт на bash: автоматизируйте поднятие необходимых контейнеров, запуск ansible-playbook и остановку контейнеров.  
Все изменения должны быть зафиксированы и отправлены в вашей личный репозиторий.  
