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
Ознакомтесь с документацией и добавьте в elasticsearch 3 индекса, в соответствии со таблицей:  
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

```

## Задача 3
В данном задании вы научитесь:  
- создавать бэкапы данных  
- восстанавливать индексы из бэкапов  
 
Создайте директорию {путь до корневой директории с elasticsearch в образе}/snapshots.  
Используя API зарегистрируйте данную директорию как snapshot repository c именем netology_backup.  
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

```
