# Домашнее задание к занятию "6.5. Elasticsearch"

## Задача 1
В этом задании вы потренируетесь в: установке elasticsearch, первоначальном конфигурировании elastcisearch, запуске elasticsearch в docker  
Используя докер образ centos:7 как базовый и документацию по установке и запуску Elastcisearch:  
- составьте Dockerfile-манифест для elasticsearch
- соберите docker-образ и сделайте push в ваш docker.io репозиторий
- запустите контейнер из получившегося образа и выполните запрос пути / c хост-машины

Требования к elasticsearch.yml:  
- данные path должны сохраняться в /var/lib
- имя ноды должно быть netology_test

В ответе приведите:  
- текст Dockerfile манифеста
- ссылку на образ в репозитории dockerhub
- ответ elasticsearch на запрос пути / в json виде (curl localhost:9200/)

Подсказки:
возможно вам понадобится установка пакета perl-Digest-SHA для корректной работы пакета shasum  
при сетевых проблемах внимательно изучите кластерные и сетевые настройки в elasticsearch.yml  
при некоторых проблемах вам поможет docker директива ulimit  
elasticsearch в логах обычно описывает проблему и пути ее решения  
Далее мы будем работать с данным экземпляром elasticsearch.  
```
Первый раз у меня все подвисло после запуска контейнера с образом ES - нехватало памяти.
Установил новую VM с Ubuntu
 Vagrant.configure("2") do |config|
 	config.vm.box = "ubuntu/bionic64"
 end
$ vagrant up
$ vagrant ssh
Установим Docker
$ sudo apt-get update
$ sudo apt -y install docker.io
$ sudo docker --version
$ sudo systemctl start docker
$ sudo systemctl status docker
Создал public репозиторий на hub.docker.com (es)
Собираем образ и пушим его в удаленный репозиторий из локального реестра
$ sudo docker build -t vladmilev/es:hw .
$ sudo docker login -u vladmilev
$ sudo docker push vladmilev/es:hw
$ sudo docker images
REPOSITORY     TAG       IMAGE ID       CREATED          SIZE
vladmilev/es   hw        ef5b46c5cdab   10 minutes ago   2.63GB
centos         7         eeb6ee3f44bd   5 months ago     204MB
Запускаем контейнер с созданным образом
$ sudo docker run -t -d -p 9200:9200 --name es_container vladmilev/es:hw
61c759ade33aac87a0b4f26c9b898b5ab32338627ff105a9699919620216f67b
$ sudo docker ps
CONTAINER ID   IMAGE             COMMAND                  CREATED          STATUS          PORTS                                       NAMES
61c759ade33a   vladmilev/es:hw   "/elasticsearch-7.11…"   16 seconds ago   Up 14 seconds   0.0.0.0:9200->9200/tcp, :::9200->9200/tcp   es_container

Запрос пути по порту 9200
$ sudo curl localhost:9200
{
  "name" : "netology_test",
  "cluster_name" : "netology_cluster",
  "cluster_uuid" : "Xp8rNQBLSu-MbnNFttzqcQ",
  "version" : {
    "number" : "7.11.1",
    "build_flavor" : "default",
    "build_type" : "tar",
    "build_hash" : "ff17057114c2199c9c1bbecc727003a907c0db7a",
    "build_date" : "2021-02-15T13:44:09.394032Z",
    "build_snapshot" : false,
    "lucene_version" : "8.7.0",
    "minimum_wire_compatibility_version" : "6.8.0",
    "minimum_index_compatibility_version" : "6.0.0-beta1"
  },
  "tagline" : "You Know, for Search"
}

Cсылка на образ в репозитории dockerhub:
https://hub.docker.com/repository/docker/vladmilev/es

Ниже представлены служебные файлы для сборки образа Dockerfile и elasticsearch.yml

Файл Dockerfile ----------------------------------------------
#6.5. Elasticsearch
FROM centos:7
LABEL ElasticSearch Homework 6.5
ENV PATH=/usr/lib:/usr/lib/jvm/jre-11/bin:$PATH

RUN yum install java-11-openjdk -y 
RUN yum install wget -y 

RUN wget https://artifacts.elastic.co/downloads/elasticsearch/elasticsearch-7.11.1-linux-x86_64.tar.gz \
    && wget https://artifacts.elastic.co/downloads/elasticsearch/elasticsearch-7.11.1-linux-x86_64.tar.gz.sha512 
RUN yum install perl-Digest-SHA -y 
RUN shasum -a 512 -c elasticsearch-7.11.1-linux-x86_64.tar.gz.sha512 \ 
    && tar -xzf elasticsearch-7.11.1-linux-x86_64.tar.gz \
    && yum upgrade -y
    
ADD elasticsearch.yml /elasticsearch-7.11.1/config/
ENV JAVA_HOME=/elasticsearch-7.11.1/jdk/
ENV ES_HOME=/elasticsearch-7.11.1
RUN groupadd elasticsearch \
    && useradd -g elasticsearch elasticsearch
    
RUN mkdir /var/lib/logs \
    && chown elasticsearch:elasticsearch /var/lib/logs \
    && mkdir /var/lib/data \
    && chown elasticsearch:elasticsearch /var/lib/data \
    && chown -R elasticsearch:elasticsearch /elasticsearch-7.11.1/
RUN mkdir /elasticsearch-7.11.1/snapshots &&\
    chown elasticsearch:elasticsearch /elasticsearch-7.11.1/snapshots
    
USER elasticsearch
CMD ["/usr/sbin/init"]
CMD ["/elasticsearch-7.11.1/bin/elasticsearch"]
----------------------------------------------------------------------
Файл elasticsearch.yml  ----------------------------------------------
# ======================== Elasticsearch Configuration =========================
#
# NOTE: Elasticsearch comes with reasonable defaults for most settings.
#       Before you set out to tweak and tune the configuration, make sure you
#       understand what are you trying to accomplish and the consequences.
#
# The primary way of configuring a node is via this file. This template lists
# the most important settings you may want to configure for a production cluster.
#
# Please consult the documentation for further information on configuration options:
# https://www.elastic.co/guide/en/elasticsearch/reference/index.html
#
# ---------------------------------- Cluster -----------------------------------
#
# Use a descriptive name for your cluster:
#
cluster.name: netology_cluster
discovery.type: single-node
#
# ------------------------------------ Node ------------------------------------
#
# Use a descriptive name for the node:
#
node.name: netology_test
#
# Add custom attributes to the node:
#
#node.attr.rack: r1
#
# ----------------------------------- Paths ------------------------------------
#
# Path to directory where to store the data (separate multiple locations by comma):
#
path.data: /var/lib/data
#
# Path to log files:
#
path.logs: /var/lib/logs

#Settings REPOSITORY PATH
#for Image from YUM (esp)
#path.repo: /usr/share/elasticsearch/snapshots
#for Image from TAR (est)
path.repo: /elasticsearch-7.11.1/snapshots
#
# ----------------------------------- Memory -----------------------------------
#
# Lock the memory on startup:
#
#bootstrap.memory_lock: true
#
# Make sure that the heap size is set to about half the memory available
# on the system and that the owner of the process is allowed to use this
# limit.
#
# Elasticsearch performs poorly when the system is swapping the memory.
#
# ---------------------------------- Network -----------------------------------
#
# Set the bind address to a specific IP (IPv4 or IPv6):
#
network.host: 0.0.0.0
#
# Set a custom port for HTTP:
#
#http.port: 9200
#
# For more information, consult the network module documentation.
#
# --------------------------------- Discovery ----------------------------------
#
# Pass an initial list of hosts to perform discovery when this node is started:
# The default list of hosts is ["127.0.0.1", "[::1]"]
#
discovery.seed_hosts: ["127.0.0.1", "[::1]"]
#
# Bootstrap the cluster using an initial set of master-eligible nodes:
#
#cluster.initial_master_nodes: ["node-1", "node-2"]
#
# For more information, consult the discovery and cluster formation module documentation.
#
# ---------------------------------- Gateway -----------------------------------
#
# Block initial recovery after a full cluster restart until N nodes are started:
#
#gateway.recover_after_nodes: 3
#
# For more information, consult the gateway module documentation.
#
# ---------------------------------- Various -----------------------------------
#
# Require explicit names when deleting indices:
#
#action.destructive_requires_name: true
----------------------------------------------------------------------
```

## Задача 2
В этом задании вы научитесь:  
- создавать и удалять индексы  
- изучать состояние кластера  
- обосновывать причину деградации доступности данных  
- 
Ознакомтесь с [документацией](https://www.elastic.co/guide/en/elasticsearch/reference/current/indices-create-index.html) и добавьте в elasticsearch 3 индекса, в соответствии со таблицей:  
```
Имя	Количество реплик	Количество шард
ind-1	0	1
ind-2	1	2
ind-3	2	4
```
Получите список индексов и их статусов, используя API и приведите в ответе на задание.  
Получите состояние кластера elasticsearch, используя API.  
Как вы думаете, почему часть индексов и кластер находится в состоянии yellow?  
Удалите все индексы.  

Важно  
При проектировании кластера elasticsearch нужно корректно рассчитывать количество реплик и шард,  
иначе возможна потеря данных индексов, вплоть до полной, при деградации системы.  
```
(1) добавить 3 индекса
vagrant@ubuntu-bionic:~$ curl -X PUT localhost:9200/ind-1 -H 'Content-Type: application/json' -d'{ "settings": { "number_of_shards": 1,  "number_of_replicas": 0 }}'
{"acknowledged":true,"shards_acknowledged":true,"index":"ind-1"}
vagrant@ubuntu-bionic:~$ curl -X PUT localhost:9200/ind-2 -H 'Content-Type: application/json' -d'{ "settings": { "number_of_shards": 2,  "number_of_replicas": 1 }}'
{"acknowledged":true,"shards_acknowledged":true,"index":"ind-2"}
vagrant@ubuntu-bionic:~$ curl -X PUT localhost:9200/ind-3 -H 'Content-Type: application/json' -d'{ "settings": { "number_of_shards": 4,  "number_of_replicas": 2 }}'
{"acknowledged":true,"shards_acknowledged":true,"index":"ind-3"}

(2) получить список индексов и статусов
vagrant@ubuntu-bionic:~$ curl -X GET 'http://localhost:9200/_cat/indices?v'
health status index uuid                   pri rep docs.count docs.deleted store.size pri.store.size
green  open   ind-1 CRsrHtC5SR-w8Uu9FyMOgw   1   0          0            0       208b           208b
yellow open   ind-3 oQgVYeaXRVyUEFImEFxXaQ   4   2          0            0       832b           832b
yellow open   ind-2 nDHgXvPHTu6Bx5Quv0K7sg   2   1          0            0       416b           416b

состояние 2 индекса
vagrant@ubuntu-bionic:~$ curl -X GET 'http://localhost:9200/_cluster/health/ind-2?pretty'
{
  "cluster_name" : "netology_cluster",
  "status" : "yellow",
  "timed_out" : false,
  "number_of_nodes" : 1,
  "number_of_data_nodes" : 1,
  "active_primary_shards" : 2,
  "active_shards" : 2,
  "relocating_shards" : 0,
  "initializing_shards" : 0,
  "unassigned_shards" : 2,
  "delayed_unassigned_shards" : 0,
  "number_of_pending_tasks" : 0,
  "number_of_in_flight_fetch" : 0,
  "task_max_waiting_in_queue_millis" : 0,
  "active_shards_percent_as_number" : 41.17647058823529
}

(3) получить состояние кластера
vagrant@ubuntu-bionic:~$ curl -XGET localhost:9200/_cluster/health/?pretty=true
{
  "cluster_name" : "netology_cluster",
  "status" : "yellow",
  "timed_out" : false,
  "number_of_nodes" : 1,
  "number_of_data_nodes" : 1,
  "active_primary_shards" : 7,
  "active_shards" : 7,
  "relocating_shards" : 0,
  "initializing_shards" : 0,
  "unassigned_shards" : 10,
  "delayed_unassigned_shards" : 0,
  "number_of_pending_tasks" : 0,
  "number_of_in_flight_fetch" : 0,
  "task_max_waiting_in_queue_millis" : 0,
  "active_shards_percent_as_number" : 41.17647058823529
}
(4) почему часть индексов и кластер находится в состоянии yellow?  
в статусе yellow индексы с назначенным числом реплик, но поскольку нет других серверов, 
куда можно эти реплики реплицировать, эта возможность реализована быть не может.

(5) удалите все индексы
vagrant@ubuntu-bionic:~$ curl -X DELETE localhost:9200/ind-1
{"acknowledged":true}
vagrant@ubuntu-bionic:~$ curl -X DELETE localhost:9200/ind-2
{"acknowledged":true}
vagrant@ubuntu-bionic:~$ curl -X DELETE localhost:9200/ind-3
{"acknowledged":true}
проверочно - смотрим  состояние индексов
vagrant@ubuntu-bionic:~$ curl -X GET localhost:9200/_cat/indices?v
health status index uuid pri rep docs.count docs.deleted store.size pri.store.size
пусто - значит все индексы удалены
```

## Задача 3
В данном задании вы научитесь:  
- создавать бэкапы данных  
- восстанавливать индексы из бэкапов  
 
Создайте директорию {путь до корневой директории с elasticsearch в образе}/snapshots.  
Используя API [зарегистрируйте](https://www.elastic.co/guide/en/elasticsearch/reference/current/snapshots-register-repository.html#snapshots-register-repository) данную директорию как snapshot repository c именем netology_backup.  
Приведите в ответе запрос API и результат вызова API для создания репозитория.  
Создайте индекс test с 0 реплик и 1 шардом и приведите в ответе список индексов.  
Создайте snapshot состояния кластера elasticsearch.  
Приведите в ответе список файлов в директории со snapshotами.  
Удалите индекс test и создайте индекс test-2. Приведите в ответе список индексов.  
Восстановите состояние кластера elasticsearch из snapshot, созданного ранее.  
Приведите в ответе запрос к API восстановления и итоговый список индексов.  

Подсказки:
возможно вам понадобится доработать elasticsearch.yml в части директивы path.repo и перезапустить elasticsearch  
```
(1) snapshot repository c именем netology_backup
- в образе создавалась директория для snapshot:
  RUN mkdir /elasticsearch-7.11.1/snapshots && chown elasticsearch:elasticsearch /elasticsearch-7.11.1/snapshots
- в настройках elasticsearch.yml указанная директория была назначена репозиторию:
  path.repo: /elasticsearch-7.11.1/snapshots
- зарегистрируем ее с именем netology_backup как snapshot repository:
vagrant@ubuntu-bionic:~$ curl -XPOST localhost:9200/_snapshot/netology_backup?pretty -H 'Content-Type: application/json' -d'{"type": "fs", "settings": { "location":"/elasticsearch-7.11.1/snapshots" }}'
{
  "acknowledged" : true
}
- проверка
vagrant@ubuntu-bionic:~$ curl -XGET http://localhost:9200/_snapshot/netology_backup?pretty
{
  "netology_backup" : {
    "type" : "fs",
    "settings" : {
      "location" : "/elasticsearch-7.11.1/snapshots"
    }
  }
}

(2) создать индекс test с 0 реплик и 1 шардом и получить список индексов
vagrant@ubuntu-bionic:~$ curl -X PUT localhost:9200/test -H 'Content-Type: application/json' -d'{ "settings": { "number_of_shards": 1,  "number_of_replicas": 0 }}'
{"acknowledged":true,"shards_acknowledged":true,"index":"test"}

vagrant@ubuntu-bionic:~$ curl -X GET localhost:9200/_cat/indices?v
health status index uuid                   pri rep docs.count docs.deleted store.size pri.store.size
green  open   test  iXegz6TKTgGWMEDk5RWw8w   1   0          0            0       208b           208b

(3) создать snapshot состояния кластера elasticsearch
vagrant@ubuntu-bionic:~$ curl -X PUT localhost:9200/_snapshot/netology_backup/elasticsearch?wait_for_completion=true

{"snapshot":{"snapshot":"elasticsearch","uuid":"yQybFjlDSeu0Em7DlByBmw","version_id":7110199,"version":"7.11.1","indices":["test"],"data_streams":[],"include_global_state":true,"state":"SUCCESS","start_time":"2022-03-07T04:17:39.276Z","start_time_in_millis":1646626659276,"end_time":"2022-03-07T04:17:39.276Z","end_time_in_millis":1646626659276,"duration_in_millis":0,"failures":[],"shards":{"total":1,"failed":0,"successful":1}}}vagrant@ubuntu-bionic:~$ {"snapshot":{"snapshot":"elasticsearch","uuid":"wixOT9zMS_WYXlGfNw7nsQ","version_id":7110199,"verlobal_state":true,"state":"SUCCESS","start_time":"2021-03-06T12:23:31.388Z","start_time_in_millis":1615033411388,"end_time":"2021-03-06T12:23:31.988Z","end_time_in_millis":1615033411988,"duration_in_millis":600,"failures":[],"shards":{"total":1,"failed":0,"successful":1}}}

(4) привести список файлов в директории со snapshotами
- запустим  bash оболочку в интерективном режиме в докер-контейнере с ES
vagrant@ubuntu-bionic:~$ sudo docker ps
CONTAINER ID   IMAGE             COMMAND                  CREATED        STATUS        PORTS                                       NAMES
61c759ade33a   vladmilev/es:hw   "/elasticsearch-7.11…"   20 hours ago   Up 20 hours   0.0.0.0:9200->9200/tcp, :::9200->9200/tcp   es_container
vagrant@ubuntu-bionic:~$ sudo docker exec -it es_container bash

[elasticsearch@61c759ade33a /]$ ls
anaconda-post.log     elasticsearch-7.11.1-linux-x86_64.tar.gz         lib    opt   sbin  usr
bin                   elasticsearch-7.11.1-linux-x86_64.tar.gz.sha512  lib64  proc  srv   var
dev                   etc                                              media  root  sys
elasticsearch-7.11.1  home                                             mnt    run   tmp
[elasticsearch@61c759ade33a /]$ cd elasticsearch-7.11.1
[elasticsearch@61c759ade33a elasticsearch-7.11.1]$ ls
LICENSE.txt  README.asciidoc  config  lib   modules  snapshots
NOTICE.txt   bin              jdk     logs  plugins
[elasticsearch@61c759ade33a elasticsearch-7.11.1]$ cd snapshots
[elasticsearch@61c759ade33a snapshots]$ ls
index-1       meta-2e8dEBQEQmi0y56M0NwQ0w.dat  snap-yQybFjlDSeu0Em7DlByBmw.dat
index.latest  meta-yQybFjlDSeu0Em7DlByBmw.dat
indices       snap-2e8dEBQEQmi0y56M0NwQ0w.dat
[elasticsearch@61c759ade33a snapshots]$ ls -lha
total 96K
drwxr-xr-x 1 elasticsearch elasticsearch 4.0K Mar  7 04:26 .
drwxr-xr-x 1 elasticsearch elasticsearch 4.0K Mar  6 08:47 ..
-rw-r--r-- 1 elasticsearch elasticsearch  633 Mar  7 04:26 index-1
-rw-r--r-- 1 elasticsearch elasticsearch    8 Mar  7 04:26 index.latest
drwxr-xr-x 3 elasticsearch elasticsearch 4.0K Mar  7 04:17 indices
-rw-r--r-- 1 elasticsearch elasticsearch  31K Mar  7 04:26 meta-2e8dEBQEQmi0y56M0NwQ0w.dat
-rw-r--r-- 1 elasticsearch elasticsearch  31K Mar  7 04:17 meta-yQybFjlDSeu0Em7DlByBmw.dat
-rw-r--r-- 1 elasticsearch elasticsearch  267 Mar  7 04:26 snap-2e8dEBQEQmi0y56M0NwQ0w.dat
-rw-r--r-- 1 elasticsearch elasticsearch  269 Mar  7 04:17 snap-yQybFjlDSeu0Em7DlByBmw.dat

(5) удалить индекс test и создать индекс test-2, вывести список индексов
vagrant@ubuntu-bionic:~$ curl -X DELETE localhost:9200/test?pretty
{
  "acknowledged" : true
}
vagrant@ubuntu-bionic:~$ curl -X PUT localhost:9200/test-2?pretty -H 'Content-Type: application/json' -d'{ "settings": { "number_of_shards": 1,  "number_of_replicas": 0 }}'
{
  "acknowledged" : true,
  "shards_acknowledged" : true,
  "index" : "test-2"
}
vagrant@ubuntu-bionic:~$ curl -X GET localhost:9200/_cat/indices?v
health status index  uuid                   pri rep docs.count docs.deleted store.size pri.store.size
green  open   test-2 Y1zyul9PQ-u3rSzLz6Lrlw   1   0          0            0       208b           208b

(6) восстановить состояние кластера elasticsearch из snapshot, созданного ранее, итоговый список индексов
vagrant@ubuntu-bionic:~$ curl -X POST localhost:9200/_snapshot/netology_backup/elasticsearch/_restore?pretty -H 'Content-Type: application/json' -d'{"include_global_state":true}'{
  "accepted" : true
}

vagrant@ubuntu-bionic:~$ curl -X GET http://localhost:9200/_cat/indices?v
health status index  uuid                   pri rep docs.count docs.deleted store.size pri.store.size
green  open   test-2 Y1zyul9PQ-u3rSzLz6Lrlw   1   0          0            0       208b           208b
green  open   test   WHnGTCLcSI-m-39PMBoM3Q   1   0          0            0       208b           208b
```
