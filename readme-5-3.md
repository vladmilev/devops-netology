# Домашнее задание к занятию "5.3. Введение. Экосистема. Архитектура. Жизненный цикл Docker контейнера"


## Задача 1
Сценарий выполения задачи:  
создайте свой репозиторий на https://hub.docker.com;  
выберете любой образ, который содержит веб-сервер Nginx;  
создайте свой fork образа;  
реализуйте функциональность: запуск веб-сервера в фоне с индекс-страницей, содержащей HTML-код ниже:  
```
<html>  
<head>  
Hey, Netology  
</head>  
<body>  
<h1>I’m DevOps Engineer!</h1>  
</body>  
</html>  
```
Опубликуйте созданный форк в своем репозитории и предоставьте ответ в виде ссылки на https://hub.docker.com/username_repo.  
```
https://hub.docker.com/repository/docker/vladmilev/nginx
(docker pull vladmilev/nginx:homework)

Последовательность действий
$mkdir task1
$nano index.html
$nano Dockerfile
---
FROM nginx

COPY index.html /usr/share/nginx/html/index.html

CMD ["nginx", "-g", "daemon off;"]
---
$docker build -t vladmilev/nginx:homework .
$docker push vladmilev/nginx:homework
```

## Задача 2
Посмотрите на сценарий ниже и ответьте на вопрос: "Подходит ли в этом сценарии использование Docker контейнеров или лучше подойдет виртуальная машина, физическая машина? Может быть возможны разные варианты?"  
Детально опишите и обоснуйте свой выбор.    
Сценарий:  
- Высоконагруженное монолитное java веб-приложение;  
- Nodejs веб-приложение;  
- Мобильное приложение c версиями для Android и iOS;  
- Шина данных на базе Apache Kafka;  
- Elasticsearch кластер для реализации логирования продуктивного веб-приложения - три ноды elasticsearch, два logstash и две ноды kibana;  
- Мониторинг-стек на базе Prometheus и Grafana;  
- MongoDB, как основное хранилище данных для java-приложения;  
- Gitlab сервер для реализации CI/CD процессов и приватный (закрытый) Docker Registry.  
```
Высоконагруженное монолитное java веб-приложение;
 - физический сервер, монолит - одно лишь приложение, а так как высоконагруженное,
 то необходим физический доступ к ресурсами для эффективной работы. 

Nodejs веб-приложение;
 - Docker, использования контейнеров позволяет настроить большое число зависимостей Nodejs приложения, изолировать и исполнять безопасно на сервере.

Мобильное приложение c версиями для Android и iOS;
 - Виртаульная машина позволяет использовать GUI для мобильных приложений. 
 
Шина данных на базе Apache Kafka;
 - для продакшен окружения, критичности используемых данных лучше подойдет Вируальная машина,
 - для тестового окружения достаточно Docker контейнеров.
 
Elasticsearch кластер для реализации логирования продуктивного веб-приложения - три ноды elasticsearch, два logstash и две ноды kibana;
 - существуют решения и для Виртуальной машины и для Docker контейнеров (в интеренете есть описания)
 - более удобным может быть использование Докера - можно ежемесячно перезапускать контейнер и получать очистку логов
   
Мониторинг-стек на базе prometheus и grafana;
 - сами программы prometheus и grafana не хранят данных, поэтому их проще развернуть в Докере,
   получая скорость развертывания и возможность масштабирования для различных задач
     
Mongodb, как основное хранилище данных для java-приложения;
 - можно использовать Виртуальную машину, т.к. это база данных
   хранить БД в Контейнере не подходит, для физического сервера - слишком расточительно
   
База данных postgresql используемая, как кэш;
  - в идеале для БД использовать Виртуалку, если доступ к кешу нужен для разных систем, пользователей, 
    использование БД на докере не рекомендуется.
    а так же должен быть сохранен между сессиями , при остановке  приклада.
    вариант с Контейнером(докер) можно рассмотреть только если данные нужны только в рамках сессии 
    самого приложения работающего на этом же контейнере.
   
Gitlab сервер для реализации CI/CD процессов и приватный (закрытый) Docker Registry
  - Docker контейнеры, поскольку на этой технологии основано построение CI/CD процессов и приватного (закрытого) Docker Registry
```

## Задача 3
- Запустите первый контейнер из образа centos c любым тэгом в фоновом режиме, подключив папку /data из текущей рабочей директории на хостовой машине в /data контейнера;
- Запустите второй контейнер из образа debian в фоновом режиме, подключив папку /data из текущей рабочей директории на хостовой машине в /data контейнера;
- Подключитесь к первому контейнеру с помощью docker exec и создайте текстовый файл любого содержания в /data;
- Добавьте еще один файл в папку /data на хостовой машине;
- Подключитесь во второй контейнер и отобразите листинг и содержание файлов в /data контейнера.
```
Запустил докер через виртуальную машину подключенную через vagrant c ubuntu:
$ sudo apt install docker.io
$ docker --version
Docker version 20.10.7, build 20.10.7-0ubuntu5~18.04.3
$ sudo systemctl start docker
$ docker status
docker: 'status' is not a docker command.
See 'docker --help'
vagrant@ubuntu-bionic:~$ sudo systemctl status docker
● docker.service - Docker Application Container Engine
   Loaded: loaded (/lib/systemd/system/docker.service; enabled; vendor preset: enabled)
   Active: active (running) since Fri 2022-01-28 04:21:39 UTC; 13min ago
     Docs: https://docs.docker.com
 Main PID: 2408 (dockerd)
    Tasks: 10
   CGroup: /system.slice/docker.service
           └─2408 /usr/bin/dockerd -H fd:// --containerd=/run/containerd/containerd.sock

1. Запустите первый контейнер из образа centos c любым тэгом в фоновом режиме, подключив папку /data из текущей рабочей директории на хостовой машине в /data контейнера;
$ sudo docker run -v /data:/data --name container_centos -d -t centos

2. Запустите второй контейнер из образа debian в фоновом режиме, подключив папку /data из текущей рабочей директории на хостовой машине в /data контейнера;
$ sudo docker run -v /data:/data --name container_debian -d -t debian

Проверим - образы
$ sudo docker images
REPOSITORY   TAG       IMAGE ID       CREATED        SIZE
debian       latest    04fbdaf87a6a   2 days ago     124MB
centos       latest    5d0da3dc9764   4 months ago   231MB

Запущенные контейнеры
$ sudo docker ps
CONTAINER ID   IMAGE     COMMAND       CREATED              STATUS              PORTS     NAMES
74f3bb163760   debian    "bash"        7 seconds ago        Up 5 seconds                  container_debian
9854ff4113a7   centos    "/bin/bash"   About a minute ago   Up About a minute             container_centos

3.Подключитесь к первому контейнеру с помощью docker exec и создайте текстовый файл любого содержания в /data;
$ sudo docker exec container_centos /bin/bash -c "echo hello world>/data/test.txt"
$ sudo docker exec container_centos /bin/bash cat /data/test.txt

4.Добавьте еще один файл в папку /data на хостовой машине;
$ sudo docker exec container_centos /bin/bash -c "echo hello world2>/data/test2.txt"
$ sudo docker exec container_centos /bin/bash ls /data

Странно почему не выводит ничего?
$ sudo docker exec -it container_centos /bin/bash
[root@561ab63437b0 /]# echo hello1>/data/test1.txt
[root@561ab63437b0 /]# ls /data
test.txt  test1.txt  test2.txt
Вроде все на месте

5.Подключитесь во второй контейнер и отобразите листинг и содержание файлов в /data контейнера
$ sudo docker exec -it container_debian /bin/bash
root@fdfa0d4acb0e:/# ls /data
test.txt  test1.txt  test2.txt
root@fdfa0d4acb0e:/# cat /data/test.txt
hello world
root@fdfa0d4acb0e:/# cat /data/test1.txt
hello1
root@fdfa0d4acb0e:/# cat /data/test2.txt
hello world2
```

## Задача 4 (*)
Воспроизвести практическую часть лекции самостоятельно.  
Соберите Docker образ с Ansible, загрузите на Docker Hub и пришлите ссылку вместе с остальными ответами к задачам.  
```
постараюсь выполнить на выходных (для зачета необязательно)
```
