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
прописываем:
# nano group_vars/deb/examp.yml
# nano group_vars/el/examp.yml
```
6. Повторите запуск playbook на окружении prod.yml. Убедитесь, что выдаются корректные значения для всех хостов.  
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
    "msg": "el default fact"
}
ok: [ubuntu] => {
    "msg": "deb default fact"
}

PLAY RECAP ************************************************************************************************************************************************
centos7                    : ok=3    changed=0    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0
ubuntu                     : ok=3    changed=0    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0
```
7. При помощи ansible-vault зашифруйте факты в group_vars/deb и group_vars/el с паролем netology.  
```
# ansible-vault encrypt group_vars/el/examp.yml group_vars/deb/examp.yml
New Vault password:
Confirm New Vault password:
Encryption successful

# cat group_vars/el/examp.yml
$ANSIBLE_VAULT;1.1;AES256
64666562616266346461653164636335396235376434626366323363343834333330663265633936
6239323866613836316637393137393862333965343061350a633261633863313565316137393431
33623039383639353834623262653037356230646562626364653036306662643966386339333134
3166323631373966360a626338623464613130623334303537623261346632383761616632323034
32646632333163613230353166633635393530316231303861326131376632323062333331643063
3966333638346363353230323331616264383636653430363661
# cat group_vars/deb/examp.yml
$ANSIBLE_VAULT;1.1;AES256
32626432666134333464663634386333616465316531666337613735623736326239366664343330
6362353638643366333165363463306135376563656337340a366639353763323039633366353234
65363431346335633361336163383730356437613534353037313264363334636137663131306462
3839303434613332610a636332623831363562313937646136666532356666653463623330346134
35316665663336356537313865386162323630656435663333666434373161613638386563613364
3663373463653331356435363130643038663361323536386365
root@ubuntu-bionic:~/netology/ansible01#
```
8. Запустите playbook на окружении prod.yml. При запуске ansible должен запросить у вас пароль. Убедитесь в работоспособности.  
```
# ansible-playbook -i inventory/prod.yml site.yml --ask-vault-pass
[DEPRECATION WARNING]: Ansible will require Python 3.8 or newer on the controller starting with Ansible 2.12. Current version: 3.6.9 (default, Mar 15
2022, 13:55:28) [GCC 8.4.0]. This feature will be removed from ansible-core in version 2.12. Deprecation warnings can be disabled by setting
deprecation_warnings=False in ansible.cfg.
Vault password:

PLAY [Print os facts] *************************************************************************************************************************************

TASK [Gathering Facts] ************************************************************************************************************************************
[DEPRECATION WARNING]: Distribution Ubuntu 18.04 on host ubuntu should use /usr/bin/python3, but is using /usr/bin/python for backward compatibility with
prior Ansible releases. A future Ansible release will default to using the discovered platform python for this host. See https://docs.ansible.com/ansible-
core/2.11/reference_appendices/interpreter_discovery.html for more information. This feature will be removed in version 2.12. Deprecation warnings can be
disabled by setting deprecation_warnings=False in ansible.cfg.
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
    "msg": "el default fact"
}
ok: [ubuntu] => {
    "msg": "deb default fact"
}

PLAY RECAP ************************************************************************************************************************************************
centos7                    : ok=3    changed=0    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0
ubuntu                     : ok=3    changed=0    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0
```
9. Посмотрите при помощи ansible-doc список плагинов для подключения. Выберите подходящий для работы на control node.  
```
https://docs.ansible.com/ansible/latest/network/getting_started/basic_concepts.html
Control node
Any machine with Ansible installed. You can run Ansible commands and playbooks by invoking the ansible or ansible-playbook command from any control node. You can use any computer that has a Python installation as a control node - laptops, shared desktops, and servers can all run Ansible. However, you cannot use a Windows machine as a control node. You can have multiple control nodes.

# ansible-doc --type=connection -l
[DEPRECATION WARNING]: Ansible will require Python 3.8 or newer on the controller starting with Ansible 2.12. Current version: 3.6.9 (default, Mar 15
2022, 13:55:28) [GCC 8.4.0]. This feature will be removed from ansible-core in version 2.12. Deprecation warnings can be disabled by setting
deprecation_warnings=False in ansible.cfg.
[WARNING]: Collection frr.frr does not support Ansible version 2.11.12
[WARNING]: Collection splunk.es does not support Ansible version 2.11.12
[WARNING]: Collection ibm.qradar does not support Ansible version 2.11.12
ansible.netcommon.httpapi      Use httpapi to run command on network appliances
ansible.netcommon.libssh       (Tech preview) Run tasks using libssh for ssh connection
ansible.netcommon.napalm       Provides persistent connection using NAPALM
ansible.netcommon.netconf      Provides a persistent connection using the netconf protocol
ansible.netcommon.network_cli  Use network_cli to run command on network appliances
ansible.netcommon.persistent   Use a persistent unix socket for connection
community.aws.aws_ssm          execute via AWS Systems Manager
community.docker.docker        Run tasks in docker containers
community.docker.docker_api    Run tasks in docker containers
community.docker.nsenter       execute on host running controller container
community.general.chroot       Interact with local chroot
community.general.funcd        Use funcd to connect to target
community.general.iocage       Run tasks in iocage jails
community.general.jail         Run tasks in jails
community.general.lxc          Run tasks in lxc containers via lxc python library
community.general.lxd          Run tasks in lxc containers via lxc CLI
community.general.qubes        Interact with an existing QubesOS AppVM
community.general.saltstack    Allow ansible to piggyback on salt minions
community.general.zone         Run tasks in a zone instance
community.kubernetes.kubectl   Execute tasks in pods running on Kubernetes
community.libvirt.libvirt_lxc  Run tasks in lxc containers via libvirt
community.libvirt.libvirt_qemu Run tasks on libvirt/qemu virtual machines
community.okd.oc               Execute tasks in pods running on OpenShift
community.vmware.vmware_tools  Execute tasks inside a VM via VMware Tools
containers.podman.buildah      Interact with an existing buildah container
containers.podman.podman       Interact with an existing podman container
kubernetes.core.kubectl        Execute tasks in pods running on Kubernetes
local                          execute on controller
paramiko_ssh                   Run tasks via python ssh (paramiko)
psrp                           Run tasks over Microsoft PowerShell Remoting Protocol
ssh                            connect via ssh client binary
winrm                          Run tasks over Microsoft's WinRM

выбираем local
```
10. В prod.yml добавьте новую группу хостов с именем local, в ней разместите localhost с необходимым типом подключения.  
```
# nano inventory/prod.yml
---
  el:
    hosts:
      centos7:
        ansible_connection: docker
  deb:
    hosts:
      ubuntu:
        ansible_connection: docker

  local:
    hosts:
      localhost:
        ansible_connection: local
```
11. Запустите playbook на окружении prod.yml. При запуске ansible должен запросить у вас пароль. Убедитесь что факты some_fact для каждого из хостов определены из верных group_vars.  
```
# ansible-playbook -i inventory/prod.yml site.yml --ask-vault-pass

TASK [Print fact] *****************************************************************************************************************************************
ok: [localhost] => {
    "msg": "all default fact"
}
ok: [centos7] => {
    "msg": "el default fact"
}
ok: [ubuntu] => {
    "msg": "deb default fact"
}

```
12. Заполните README.md ответами на вопросы. Сделайте git push в ветку master. В ответе отправьте ссылку на ваш открытый репозиторий с изменённым playbook и заполненным README.md.  
```
# git add ansible01/*
# git status
On branch main
Your branch is up to date with 'origin/main'.

Changes to be committed:
  (use "git reset HEAD <file>..." to unstage)

        modified:   ansible01/README.md
        modified:   ansible01/group_vars/all/examp.yml
        modified:   ansible01/group_vars/deb/examp.yml
        modified:   ansible01/group_vars/el/examp.yml
        modified:   ansible01/inventory/prod.yml

Untracked files:
  (use "git add <file>..." to include in what will be committed)

        mnt-homeworks/
# git commit -m "homework 8.1 done"
$ git push

Ссылка на репозиторий: https://github.com/vladmilev/ansible/tree/main/ansible01
```

## Необязательная часть  
При помощи ansible-vault расшифруйте все зашифрованные файлы с переменными.  
Зашифруйте отдельное значение PaSSw0rd для переменной some_fact паролем netology. Добавьте полученное значение в group_vars/all/exmp.yml.  
Запустите playbook, убедитесь, что для нужных хостов применился новый fact.  
Добавьте новую группу хостов fedora, самостоятельно придумайте для неё переменную. В качестве образа можно использовать этот.  
Напишите скрипт на bash: автоматизируйте поднятие необходимых контейнеров, запуск ansible-playbook и остановку контейнеров.  
Все изменения должны быть зафиксированы и отправлены в вашей личный репозиторий.  
