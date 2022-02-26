# Домашнее задание к занятию "6.4. PostgreSQL"

## Задача 1
Используя docker поднимите инстанс PostgreSQL (версию 13). Данные БД сохраните в volume.  
Подключитесь к БД PostgreSQL используя psql.  
Воспользуйтесь командой \? для вывода подсказки по имеющимся в psql управляющим командам.  
Найдите и приведите управляющие команды для:  
- вывода списка БД
- подключения к БД
- вывода списка таблиц
- вывода описания содержимого таблиц
- выхода из psql
```
vagrant@ubuntu-bionic:~$ sudo docker pull postgres:13
vagrant@ubuntu-bionic:~$ sudo docker volume create vol_postgres
vagrant@ubuntu-bionic:~$ sudo docker run --rm --name pg-docker -e POSTGRES_PASSWORD=postgres -ti -p 5432:5432 -v vol_postgres:/var/lib/postgresql/data postgres:13
Открыл еще одну сессию (vagrant ssh)
vagrant@ubuntu-bionic:~$ sudo docker exec -it pg-docker bash

root@090cfd68d03b:/# psql -h localhost -p 5432 -U postgres
psql (13.6 (Debian 13.6-1.pgdg110+1))
Type "help" for help.
postgres=#\?
  \dt[S+] [PATTERN]      list tables
  \dT[S+] [PATTERN]      list data types
  \du[S+] [PATTERN]      list roles
  \dv[S+] [PATTERN]      list views
  \dx[+]  [PATTERN]      list extensions
  \dy[+]  [PATTERN]      list event triggers
  \l[+]   [PATTERN]      list databases
  
- вывод списка БД\l
postgres=# \l
                                 List of databases
   Name    |  Owner   | Encoding |  Collate   |   Ctype    |   Access privileges
-----------+----------+----------+------------+------------+-----------------------
 postgres  | postgres | UTF8     | en_US.utf8 | en_US.utf8 |
 template0 | postgres | UTF8     | en_US.utf8 | en_US.utf8 | =c/postgres          +
           |          |          |            |            | postgres=CTc/postgres
 template1 | postgres | UTF8     | en_US.utf8 | en_US.utf8 | =c/postgres          +
           |          |          |            |            | postgres=CTc/postgres
(3 rows)

- подключения к БД
  \c[onnect] {[DBNAME|- USER|- HOST|- PORT|-] | conninfo}
                         connect to new database (currently "postgres")
postgres=# \c postgres
You are now connected to database "postgres" as user "postgres".

- вывод списка таблиц
  \dt[S+] [PATTERN]      list tables

postgres-# \dt
Did not find any relations.
postgres-# \dtS
                    List of relations
   Schema   |          Name           | Type  |  Owner
------------+-------------------------+-------+----------
 pg_catalog | pg_aggregate            | table | postgres
 pg_catalog | pg_am                   | table | postgres
 pg_catalog | pg_amop                 | table | postgres
...
- вывода описания содержимого таблиц
 \d[S+]                 list tables, views, and sequences
postgres-# \d pg_database
               Table "pg_catalog.pg_database"
    Column     |   Type    | Collation | Nullable | Default
---------------+-----------+-----------+----------+---------
 oid           | oid       |           | not null |
 datname       | name      |           | not null |
 datdba        | oid       |           | not null |
 encoding      | integer   |           | not null |
 datcollate    | name      |           | not null |
 datctype      | name      |           | not null |
 datistemplate | boolean   |           | not null |
 datallowconn  | boolean   |           | not null |
 datconnlimit  | integer   |           | not null |
 datlastsysoid | oid       |           | not null |
 datfrozenxid  | xid       |           | not null |
 datminmxid    | xid       |           | not null |
 dattablespace | oid       |           | not null |
 datacl        | aclitem[] |           |          |
Indexes:
    "pg_database_datname_index" UNIQUE, btree (datname), tablespace "pg_global"
    "pg_database_oid_index" UNIQUE, btree (oid), tablespace "pg_global"
Tablespace: "pg_global"

- выхода из psql
postgres-# \q
root@090cfd68d03b:/#
```

## Задача 2
Используя psql создайте БД test_database.  
Изучите бэкап БД. Восстановите бэкап БД в test_database.  
Перейдите в управляющую консоль psql внутри контейнера.  
Подключитесь к восстановленной БД и проведите операцию ANALYZE для сбора статистики по таблице.  
Используя таблицу pg_stats, найдите столбец таблицы orders с наибольшим средним значением размера элементов в байтах.  
Приведите в ответе команду, которую вы использовали для вычисления и полученный результат.  
```
root@090cfd68d03b:/# psql -h localhost -p 5432 -U postgres
psql (13.6 (Debian 13.6-1.pgdg110+1))
Type "help" for help.

postgres=# CREATE DATABASE test_database;
CREATE DATABASE

Вышел из докера и командной оболочки bash создал на виртуальной машине дамп-файл test_dump.sql
vagrant@ubuntu-bionic:~$ nano test_dump.sql

Смотрим название нашего контейнера
vagrant@ubuntu-bionic:~$ sudo docker container list -a
CONTAINER ID   IMAGE           COMMAND                  CREATED          STATUS                  PORTS                                       NAMES
090cfd68d03b   postgres:13     "docker-entrypoint.s…"   34 minutes ago   Up 34 minutes           0.0.0.0:5432->5432/tcp, :::5432->5432/tcp   pg-docker

Запускаем дамп на выполнение в контейнере через psql
vagrant@ubuntu-bionic:~$ sudo cat test_dump.sql | sudo docker exec -i  pg-docker psql -U postgres test_database
SET
SET
SET
SET
SET
 set_config
------------

(1 row)

SET
SET
SET
SET
SET
SET
CREATE TABLE
ALTER TABLE
CREATE SEQUENCE
ALTER TABLE
ALTER SEQUENCE
ALTER TABLE
COPY 8
 setval
--------
      8
(1 row)

ALTER TABLE

Вошел снова в докер с командной оболочкой
vagrant@ubuntu-bionic:~$ sudo docker exec -it pg-docker bash

Запускаю управляющую консоль psql внутри контейнера
root@090cfd68d03b:/# psql -h localhost -p 5432 -U postgres
psql (13.6 (Debian 13.6-1.pgdg110+1))

postgres=# \c test_database
You are now connected to database "test_database" as user "postgres".

test_database=# \dt
         List of relations
 Schema |  Name  | Type  |  Owner
--------+--------+-------+----------
 public | orders | table | postgres
(1 row)

test_database=# ANALYSE VERBOSE public.orders;
INFO:  analyzing "public.orders"
INFO:  "orders": scanned 1 of 1 pages, containing 8 live rows and 0 dead rows; 8 rows in sample, 8 estimated total rows
ANALYZE

test_database=# SELECT avg_width FROM pg_stats WHERE tablename='orders';
 avg_width
-----------
         4
        16
         4
(3 rows)
```

## Задача 3
Архитектор и администратор БД выяснили, что ваша таблица orders разрослась до невиданных размеров   
и поиск по ней занимает долгое время. Вам, как успешному выпускнику курсов DevOps в нетологии     
предложили провести разбиение таблицы на 2 (шардировать на orders_1 - price>499 и orders_2 - price<=499).  
Предложите SQL-транзакцию для проведения данной операции.  

Можно ли было изначально исключить "ручное" разбиение при проектировании таблицы orders?
```
test_database=# SELECT * FROM orders;
 id |        title         | price
----+----------------------+-------
  1 | War and peace        |   100
  2 | My little database   |   500
  3 | Adventure psql time  |   300
  4 | Server gravity falls |   300
  5 | Log gossips          |   123
  6 | WAL never lies       |   900
  7 | Me and my bash-pet   |   499
  8 | Dbiezdmin            |   501
(8 rows)

Шардирование (горизонтальное партиционирование) — это принцип проектирования базы данных, 
при котором логически независимые строки таблицы базы данных хранятся раздельно

Преобразуем существующую таблицу в партиционированную путем пересоздания
test_database=# alter table orders rename to orders_backup;
ALTER TABLE
test_database=# create table orders (id integer, title varchar(80), price integer) partition by range(price);
CREATE TABLE
test_database=# create table orders_2 partition of orders for values from (0) to (499);
CREATE TABLE
test_database=# create table orders_1 partition of orders for values from (499) to (999999999);
CREATE TABLE
test_database=# insert into orders (id, title, price) select * from orders_backup;
INSERT 0 8

Ссылка на документацию https://postgrespro.ru/docs/postgresql/12/sql-createtable

Действительно, можно было исключить "ручное" разбиение таблицы,
если изначально сделать ее секционированной, указав опцию PARTITION BY с указанным диапазоном разбиения RANGE

Сама по себе секционируемая таблица не содержит данных. Строка данных, вставляемая в эту таблицу, 
перенаправляется в секцию в зависимости от значений столбцов или выражений в ключе разбиения. Если
значениям в новой строке не соответствует ни одна из существующих секций, возникает ошибка.
```

## Задача 4
Используя утилиту pg_dump создайте бекап БД test_database.  
Как бы вы доработали бэкап-файл, чтобы добавить уникальность значения столбца title для таблиц test_database?  
```

```
