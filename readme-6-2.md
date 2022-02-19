# Домашнее задание к занятию "6.2. SQL"

## Задача 1
Используя docker поднимите инстанс PostgreSQL (версию 12) c 2 volume, в который будут складываться данные БД и бэкапы.

Приведите получившуюся команду или docker-compose манифест.
```
Источник: "Запускаем PostgreSQL в Docker: от простого к сложному" https://habr.com/ru/post/578744/

docker-compose.yaml
-------------------
version: "2.1"
services:
  postgres:
    image: postgres:12.0
    restart: always
    environment:
      POSTGRES_DB: "test_db"
      POSTGRES_USER: "postgres"
      POSTGRES_PASSWORD: "postgres"
      PGDATA: "/var/lib/postgresql/data/pgdata"
    volumes:
      - ../sql-init-scripts:/docker-entrypoint-initdb.d
      - .:/var/lib/postgresql/data
    ports:
      - "5432:5432"
---------------------
$ sudo docker-compose up -d
$ psql -h 127.0.0.1 -U admin -d test_db
test_db=# \l
                             List of databases
   Name    | Owner | Encoding |  Collate   |   Ctype    | Access privileges
-----------+-------+----------+------------+------------+-------------------
 postgres  | admin | UTF8     | en_US.utf8 | en_US.utf8 |
 template0 | admin | UTF8     | en_US.utf8 | en_US.utf8 | =c/admin         +
           |       |          |            |            | admin=CTc/admin
 template1 | admin | UTF8     | en_US.utf8 | en_US.utf8 | =c/admin         +
           |       |          |            |            | admin=CTc/admin
 test_db   | admin | UTF8     | en_US.utf8 | en_US.utf8 |
(4 rows)
```

## Задача 2
В БД из задачи 1:  
- создайте пользователя test-admin-user и БД test_db  
- в БД test_db создайте таблицу orders и clients (спeцификация таблиц ниже)  
- предоставьте привилегии на все операции пользователю test-admin-user на таблицы БД test_db  
- создайте пользователя test-simple-user  
- предоставьте пользователю test-simple-user права на SELECT/INSERT/UPDATE/DELETE данных таблиц БД test_db  

Таблица orders:
- id (serial primary key)
- наименование (string)
- цена (integer)

Таблица clients:
- id (serial primary key)
- фамилия (string)
- страна проживания (string, index)
- заказ (foreign key orders)

Приведите:  
- итоговый список БД после выполнения пунктов выше,  
- описание таблиц (describe)  
- SQL-запрос для выдачи списка пользователей с правами над таблицами test_db  
- список пользователей с правами над таблицами test_db  
```
CREATE ROLE "test-admin-user" SUPERUSER NOCREATEDB NOCREATEROLE NOINHERIT LOGIN;
GRANT ALL ON TABLE public.clients TO "test-admin-user";
GRANT ALL ON TABLE public.orders TO "test-admin-user";
  
CREATE TABLE orders (id integer, name text, price integer, PRIMARY KEY (id));
CREATE TABLE clients (id integer PRIMARY KEY,	lastname text, country text, booking integer,	FOREIGN KEY (booking) REFERENCES orders (Id));

CREATE ROLE "test-simple-user" NOSUPERUSER NOCREATEDB NOCREATEROLE NOINHERIT LOGIN;
GRANT SELECT ON TABLE public.clients TO "test-simple-user";
GRANT INSERT ON TABLE public.clients TO "test-simple-user";
GRANT UPDATE ON TABLE public.clients TO "test-simple-user";
GRANT DELETE ON TABLE public.clients TO "test-simple-user";
GRANT SELECT ON TABLE public.orders TO "test-simple-user";
GRANT INSERT ON TABLE public.orders TO "test-simple-user";
GRANT UPDATE ON TABLE public.orders TO "test-simple-user";
GRANT DELETE ON TABLE public.orders TO "test-simple-user";

Описание всех таблиц
test_db=# \dt+
                      List of relations
 Schema |  Name   | Type  | Owner |    Size    | Description
--------+---------+-------+-------+------------+-------------
 public | clients | table | admin | 8192 bytes |
 public | orders  | table | admin | 8192 bytes |
(2 rows)
 
 список всех пользователей и их привилегий
 test_db=# \du
                                      List of roles
    Role name    |                         Attributes                         | Member of
-----------------+------------------------------------------------------------+-----------
 admin           | Superuser, Create role, Create DB, Replication, Bypass RLS | {}
 test-admin-user | Superuser, No inheritance                                  | {}

select * from information_schema.table_privileges where grantee in ('test-admin-user', 'test-simple-user');

 grantor |     grantee      | table_catalog | table_schema | table_name | privilege_type | is_grantable | with_hierarchy
---------+------------------+---------------+--------------+------------+----------------+--------------+----------------
 admin   | test-simple-user | test_db       | public       | orders     | INSERT         | NO           | NO
 admin   | test-simple-user | test_db       | public       | orders     | SELECT         | NO           | YES
 admin   | test-simple-user | test_db       | public       | orders     | UPDATE         | NO           | NO
 admin   | test-simple-user | test_db       | public       | orders     | DELETE         | NO           | NO
 admin   | test-admin-user  | test_db       | public       | orders     | INSERT         | YES          | NO
 admin   | test-admin-user  | test_db       | public       | orders     | SELECT         | YES          | YES
 admin   | test-admin-user  | test_db       | public       | orders     | UPDATE         | YES          | NO
 admin   | test-admin-user  | test_db       | public       | orders     | DELETE         | YES          | NO
 admin   | test-admin-user  | test_db       | public       | orders     | TRUNCATE       | YES          | NO
 admin   | test-admin-user  | test_db       | public       | orders     | REFERENCES     | YES          | NO
 admin   | test-admin-user  | test_db       | public       | orders     | TRIGGER        | YES          | NO
 admin   | test-simple-user | test_db       | public       | clients    | INSERT         | NO           | NO
 admin   | test-simple-user | test_db       | public       | clients    | SELECT         | NO           | YES
 admin   | test-simple-user | test_db       | public       | clients    | UPDATE         | NO           | NO
 admin   | test-simple-user | test_db       | public       | clients    | DELETE         | NO           | NO
 admin   | test-admin-user  | test_db       | public       | clients    | INSERT         | YES          | NO
 admin   | test-admin-user  | test_db       | public       | clients    | SELECT         | YES          | YES
 admin   | test-admin-user  | test_db       | public       | clients    | UPDATE         | YES          | NO
 admin   | test-admin-user  | test_db       | public       | clients    | DELETE         | YES          | NO
 admin   | test-admin-user  | test_db       | public       | clients    | TRUNCATE       | YES          | NO
 admin   | test-admin-user  | test_db       | public       | clients    | REFERENCES     | YES          | NO
 admin   | test-admin-user  | test_db       | public       | clients    | TRIGGER        | YES          | NO
(22 rows)


```

## Задача 3
Используя SQL синтаксис - наполните таблицы следующими тестовыми данными:  
Таблица orders  
Наименование	цена  
Шоколад	10  
Принтер	3000  
Книга	500  
Монитор	7000  
Гитара	4000  

Таблица clients  
ФИО	Страна проживания  
Иванов Иван Иванович	USA  
Петров Петр Петрович	Canada  
Иоганн Себастьян Бах	Japan  
Ронни Джеймс Дио	Russia  
Ritchie Blackmore	Russia  

Используя SQL синтаксис:  
- вычислите количество записей для каждой таблицы  
- приведите в ответе: 
   - запросы  
   - результаты их выполнения.  
```

```

## Задача 4
Часть пользователей из таблицы clients решили оформить заказы из таблицы orders.

Используя foreign keys свяжите записи из таблиц, согласно таблице:  
 ФИО	Заказ  
Иванов Иван Иванович	Книга  
Петров Петр Петрович	Монитор  
Иоганн Себастьян Бах	Гитара  

Приведите SQL-запросы для выполнения данных операций.  
Приведите SQL-запрос для выдачи всех пользователей, которые совершили заказ, а также вывод данного запроса.  
Подсказк - используйте директиву UPDATE.  
```

```

## Задача 5
Получите полную информацию по выполнению запроса выдачи всех пользователей из задачи 4 (используя директиву EXPLAIN).

Приведите получившийся результат и объясните что значат полученные значения.
```

```

## Задача 6
Создайте бэкап БД test_db и поместите его в volume, предназначенный для бэкапов (см. Задачу 1).  
Остановите контейнер с PostgreSQL (но не удаляйте volumes).  
Поднимите новый пустой контейнер с PostgreSQL.  
Восстановите БД test_db в новом контейнере.  
Приведите список операций, который вы применяли для бэкапа данных и восстановления.  
```

```
