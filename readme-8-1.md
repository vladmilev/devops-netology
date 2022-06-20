# Домашнее задание к занятию "08.01 Введение в Ansible"

## Подготовка к выполнению  
- Установите ansible версии 2.10 или выше.  
- Создайте свой собственный публичный репозиторий на github с произвольным именем.  
- Скачайте playbook из репозитория с домашним заданием и перенесите его в свой репозиторий.  
```
создал новый репозиторий под это задание - ansible, каталог под домашку 01
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
```

## Основная часть  
1. Попробуйте запустить playbook на окружении из test.yml, зафиксируйте какое значение имеет факт some_fact для указанного хоста при выполнении playbook'a.  
```

```
2. Найдите файл с переменными (group_vars) в котором задаётся найденное в первом пункте значение и поменяйте его на 'all default fact'.  
```

```
3. Воспользуйтесь подготовленным (используется docker) или создайте собственное окружение для проведения дальнейших испытаний.  
```

```
4. Проведите запуск playbook на окружении из prod.yml. Зафиксируйте полученные значения some_fact для каждого из managed host.  
```

```
5. Добавьте факты в group_vars каждой из групп хостов так, чтобы для some_fact получились следующие значения: для deb - 'deb default fact', для el - 'el default fact'.  
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
