# Домашнее задание к занятию "09.05 Gitlab"

## Подготовка к выполнению
Необходимо зарегистрироваться https://about.gitlab.com/free-trial/   
Создайте свой новый проект  
Создайте новый репозиторий в gitlab, наполните его файлами https://github.com/netology-code/mnt-homeworks/blob/master/09-ci-05-gitlab/repository    
Проект должен быть публичным, остальные настройки по желанию  


## Основная часть  
## DevOps  
В репозитории содержится код проекта на python. Проект - RESTful API сервис. Ваша задача автоматизировать сборку образа с выполнением python-скрипта:
Образ собирается на основе centos:7  
Python версии не ниже 3.7  
Установлены зависимости: flask flask-jsonpify flask-restful  
Создана директория /python_api  
Скрипт из репозитория размещён в /python_api  
Точка вызова: запуск скрипта  
Если сборка происходит на ветке master: Образ должен пушится в docker registry вашего gitlab python-api:latest, иначе этот шаг нужно пропустить  
```
1. добавил пайплайн сборки образа докер-контейнера
.gitlab-ci.yml
докер-контейнер запускает образ centos запускает в нем докер как сервис, копирует питоновский скрипт и запускает его как точку входа
Dockerfile
2. commit -> сразу запустился сначала stage:build (закоммичена новая ветка) затем stage:deploy (смержена в main)
3. собранный образ находится в Packages & Registries \ Container Registry
ссылка registry.gitlab.com/vlad_milev/mnt-ci/python-api (146 Мб)
Проверка работы
- поднял vargant c may1
vagrant up
vagrant ssh
$ docker -v
20.10.7
$ sudo docker pull registry.gitlab.com/vlad_milev/mnt-ci/python-api
$ sudo docker run -d -p 5290:5290 registry.gitlab.com/vlad_milev/mnt-ci/python-api
vagrant@ubuntu-bionic:~$ curl localhost:5290/get_info
{"version": 3, "method": "GET", "message": "Already started"}
```

## Product Owner
Вашему проекту нужна бизнесовая доработка: необходимо поменять JSON ответа на вызов метода GET /rest/api/get_info, необходимо создать Issue в котором указать:  
Какой метод необходимо исправить  
Текст с { "message": "Already started" } на { "message": "Running"}  
Issue поставить label: feature  
```
https://gitlab.com/vlad_milev/mnt-ci/-/issues/1
```

## Developer
Вам пришел новый Issue на доработку, вам необходимо:  
Создать отдельную ветку, связанную с этим issue  
Внести изменения по тексту из задания  
Подготовить Merge Requst, влить необходимые изменения в master, проверить, что сборка прошла успешно  
```
Создал ветку из самой задачи (issue) 
Для входа в IDE зашел в ветку репозитория и нажал кнопку
Сделал изменение ответа и commit
MR на ветку и merge
сборки запускались автоматически
```

## Tester
Разработчики выполнили новый Issue, необходимо проверить валидность изменений:  
Поднять докер-контейнер с образом python-api:latest и проверить возврат метода на корректность  
Закрыть Issue с комментарием об успешности прохождения, указав желаемый результат и фактически достигнутый  
```
$ sudo -s
root@ubuntu-bionic:~# docker ps
CONTAINER ID   IMAGE          COMMAND                  CREATED          STATUS          PORTS                                       NAMES
ad68f884c2ef   c9404ab62bca   "python3 /python_api…"   31 minutes ago   Up 31 minutes   0.0.0.0:5290->5290/tcp, :::5290->5290/tcp   nervous_mirzakhani

root@ubuntu-bionic:~# docker stop ad68f884c2ef
ad68f884c2ef
root@ubuntu-bionic:~# docker rm ad68f884c2ef
ad68f884c2ef

root@ubuntu-bionic:~#  docker run -d -p 5290:5290 registry.gitlab.com/vlad_milev/mnt-ci/python-api
dc8c5ae3ff73ffee813097fb46b4f055a40a41db41c4dd248e42fc0eb429d43e

root@ubuntu-bionic:~#  curl localhost:5290/get_info
{"version": 3, "method": "GET", "message": "Running"}
```

## Итог
После успешного прохождения всех ролей - отправьте ссылку на ваш проект в гитлаб, как решение домашнего задания

## Необязательная часть
Автомазируйте работу тестировщика, пусть у вас будет отдельный конвейер, который автоматически поднимает контейнер и выполняет проверку, например, при помощи curl. На основе вывода - будет приниматься решение об успешности прохождения тестирования
