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
insert into orders VALUES (1, 'Шоколад', 10), (2, 'Принтер', 3000), (3, 'Книга', 500), (4, 'Монитор', 7000), (5, 'Гитара', 4000);
select * from orders;
 id |  name   | price
----+---------+-------
  1 | Шоколад |    10
  2 | Принтер |  3000
  3 | Книга   |   500
  4 | Монитор |  7000
  5 | Гитара  |  4000
(5 rows)
insert into clients VALUES (1, 'Иванов Иван Иванович', 'USA'), (2, 'Петров Петр Петрович', 'Canada'), (3, 'Иоганн Себастьян Бах', 'Japan'), (4, 'Ронни Джеймс Дио', 'Russia'), (5, 'Ritchie Blackmore', 'Russia');
INSERT 0 5
select * from clients;
 id |       lastname       | country | booking
----+----------------------+---------+---------
  1 | Иванов Иван Иванович | USA     |
  2 | Петров Петр Петрович | Canada  |
  3 | Иоганн Себастьян Бах | Japan   |
  4 | Ронни Джеймс Дио     | Russia  |
  5 | Ritchie Blackmore    | Russia  |
(5 rows)
test_db=# select count (*) from orders;
 count
-------
     5
(1 row)
test_db=# select count (*) from clients;
 count
-------
     5
(1 row)
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
update  clients set booking = 3 where id = 1;
update  clients set booking = 4 where id = 2;
update  clients set booking = 5 where id = 3;

test_db=# select * from clients c inner join orders o on c.booking=o.id;
 id |       lastname       | country | booking | id |  name   | price
----+----------------------+---------+---------+----+---------+-------
  1 | Иванов Иван Иванович | USA     |       3 |  3 | Книга   |   500
  2 | Петров Петр Петрович | Canada  |       4 |  4 | Монитор |  7000
  3 | Иоганн Себастьян Бах | Japan   |       5 |  5 | Гитара  |  4000
(3 rows)

```

## Задача 5
Получите полную информацию по выполнению запроса выдачи всех пользователей из задачи 4 (используя директиву EXPLAIN).

Приведите получившийся результат и объясните что значат полученные значения.
```
test_db=# explain select * from clients c inner join orders o on c.booking=o.id;
                               QUERY PLAN
-------------------------------------------------------------------------
 Hash Join  (cost=37.00..57.24 rows=810 width=112)
   Hash Cond: (c.booking = o.id)
   ->  Seq Scan on clients c  (cost=0.00..18.10 rows=810 width=72)
   ->  Hash  (cost=22.00..22.00 rows=1200 width=40)
         ->  Seq Scan on orders o  (cost=0.00..22.00 rows=1200 width=40)
(5 rows)

Эта команда выводит план выполнения, генерируемый планировщиком PostgreSQL для заданного оператора. План выполнения показывает, как будут сканироваться таблицы, затрагиваемые оператором — просто последовательно, по индексу и т. д. — а если запрос связывает несколько таблиц, какой алгоритм соединения будет выбран для объединения считанных из них строк.

Наибольший интерес в выводимой информации представляет ожидаемая стоимость выполнения оператора, которая показывает, сколько, по мнению планировщика, будет выполняться этот оператор (это значение измеряется в единицах стоимости, которые не имеют точного определения, но обычно это обращение к странице на диске). Фактически выводятся два числа: стоимость запуска до выдачи первой строки и общая стоимость выдачи всех строк. Для большинства запросов важна общая стоимость, но в таких контекстах, как подзапрос в EXISTS, планировщик будет минимизировать стоимость запуска, а не общую стоимость (так как исполнение запроса всё равно завершится сразу после получения одной строки). Кроме того, если количество возвращаемых строк ограничивается предложением LIMIT, планировщик интерполирует стоимость между двумя этими числами, выбирая наиболее выгодный план.
```

## Задача 6
Создайте бэкап БД test_db и поместите его в volume, предназначенный для бэкапов (см. Задачу 1).  
Остановите контейнер с PostgreSQL (но не удаляйте volumes).  
Поднимите новый пустой контейнер с PostgreSQL.  
Восстановите БД test_db в новом контейнере.  
Приведите список операций, который вы применяли для бэкапа данных и восстановления.  
```

```
