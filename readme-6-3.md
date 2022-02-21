# Домашнее задание к занятию "6.3. MySQL"

## Задача 1
Используя docker поднимите инстанс MySQL (версию 8). Данные БД сохраните в volume.  
Изучите бэкап БД и восстановитесь из него.  
Перейдите в управляющую консоль mysql внутри контейнера.  
Используя команду \h получите список управляющих команд.  
Найдите команду для выдачи статуса БД и приведите в ответе из ее вывода версию сервера БД.  
Подключитесь к восстановленной БД и получите список таблиц из этой БД.  
Приведите в ответе количество записей с price > 300.  
В следующих заданиях мы будем продолжать работу с данным контейнером.  
```
vagrant@ubuntu-bionic:~$ mkdir docker-mysql
vagrant@ubuntu-bionic:~$ cd docker-mysql
  Вставил дамп через буфер обмена в файл test_dump.sql
vagrant@ubuntu-bionic:~/docker-mysql$ nano test_dump.sql
  Скачал образ MySQL (версию 8)
vagrant@ubuntu-bionic:~/docker-mysql$ sudo docker pull mysql:8.0
Status: Downloaded newer image for mysql:8.0
  Создал volume
vagrant@ubuntu-bionic:~/docker-mysql$ sudo docker volume create vol_mysql
vol_mysql
  Запуск докер-контейнера
vagrant@ubuntu-bionic:~/docker-mysql$ sudo docker run --rm --name mysql-docker -e MYSQL_ROOT_PASSWORD=mysql -ti 
-p 3306:3306 -vl_mysql:/etc/mysql mysql:8.0
  Подключил еще одну сессию vagrant ssh
vagrant@ubuntu-bionic:~$ sudo docker container list -a
CONTAINER ID   IMAGE           COMMAND                  CREATED         STATUS                    PORTS                                                  NAMES
a435f793d39a   mysql:8.0       "docker-entrypoint.s…"   3 minutes ago   Up 3 minutes              0.0.0.0:3306->3306/tcp, :::3306->3306/tcp, 33060/tcp   mysql-docker
0b51034fab0a   postgres:12.0   "docker-entrypoint.s…"   4 days ago      Exited (0) 47 hours ago                                                          dockersql_postgres_1
fdfa0d4acb0e   debian          "bash"                   3 weeks ago     Up 3 weeks                                                                       container_debian
561ab63437b0   centos          "/bin/bash"              3 weeks ago     Up 3 weeks                                                                       container_centos
vagrant@ubuntu-bionic:~$ cd docker-mysql
  Подключаемся к mysql и создаем БД
vagrant@ubuntu-bionic:~/docker-mysql$ mysql -h 127.0.0.1 -u root -p mysql
mysql> create database test_db;
Query OK, 1 row affected (0.02 sec)
  Восстановим дамп
vagrant@ubuntu-bionic:~/docker-mysql$ sudo cat test_dump.sql | sudo docker exec -i mysql-docker mysql -u root test_db
vagrant@ubuntu-bionic:~/docker-mysql$ mysql -h 127.0.0.1 -u root -p mysql
  Cписок управляющих команд \h
  List of all MySQL commands:
Note that all text commands must be first on line and end with ';'
?         (\?) Synonym for `help'.
clear     (\c) Clear the current input statement.
connect   (\r) Reconnect to the server. Optional arguments are db and host.
delimiter (\d) Set statement delimiter.
edit      (\e) Edit command with $EDITOR.
ego       (\G) Send command to mysql server, display result vertically.
exit      (\q) Exit mysql. Same as quit.
go        (\g) Send command to mysql server.
help      (\h) Display this help.
nopager   (\n) Disable pager, print to stdout.
notee     (\t) Don't write into outfile.
pager     (\P) Set PAGER [to_pager]. Print the query results via PAGER.
print     (\p) Print current command.
prompt    (\R) Change your mysql prompt.
quit      (\q) Quit mysql.
rehash    (\#) Rebuild completion hash.
source    (\.) Execute an SQL script file. Takes a file name as an argument.
status    (\s) Get status information from the server.
system    (\!) Execute a system shell command.
tee       (\T) Set outfile [to_outfile]. Append everything into given outfile.
use       (\u) Use another database. Takes database name as argument.
charset   (\C) Switch to another charset. Might be needed for processing binlog with multi-byte charsets.
warnings  (\W) Show warnings after every statement.
nowarning (\w) Don't show warnings after every statement.
resetconnection(\x) Clean session context.

 Команда для выдачи статуса БД и приведите в ответе из ее вывода версию сервера БД.  
mysql> \s
--------------
mysql  Ver 14.14 Distrib 5.7.37, for Linux (x86_64) using  EditLine wrapper

Connection id:          16
Current database:       mysql
Current user:           root@172.17.0.1
SSL:                    Cipher in use is ECDHE-RSA-AES128-GCM-SHA256
Current pager:          stdout
Using outfile:          ''
Using delimiter:        ;
Server version:         8.0.28 MySQL Community Server - GPL
Protocol version:       10
Connection:             127.0.0.1 via TCP/IP
Server characterset:    utf8mb4
Db     characterset:    utf8mb4
Client characterset:    utf8mb3
Conn.  characterset:    utf8mb3
TCP port:               3306
Uptime:                 41 min 20 sec

  Подключитесь к восстановленной БД и получите список таблиц из этой БД.  
mysql> use test_db;
Database changed
mysql> show tables;
+-------------------+
| Tables_in_test_db |
+-------------------+
| orders            |
+-------------------+
1 row in set (0.00 sec)

  Приведите в ответе количество записей с price > 300.  
mysql> select count(*) from orders where price>300;
+----------+
| count(*) |
+----------+
|        1 |
+----------+
1 row in set (0.00 sec)
```

## Задача 2
Создайте пользователя test в БД c паролем test-pass, используя:  
- плагин авторизации mysql_native_password
- срок истечения пароля - 180 дней
- количество попыток авторизации - 3
- максимальное количество запросов в час - 100
- аттрибуты пользователя:
 Фамилия "Pretty"
 Имя "James"
- Предоставьте привелегии пользователю test на операции SELECT базы test_db.

Используя таблицу INFORMATION_SCHEMA.USER_ATTRIBUTES получите данные по пользователю test и приведите в ответе к задаче.
```
mysql> CREATE USER 'test'@'localhost' IDENTIFIED BY 'test-pass';
Query OK, 0 rows affected (0.03 sec)

mysql> ALTER USER 'test'@'localhost' ATTRIBUTE '{"fname":"James","lname":"Pretty"}';
Query OK, 0 rows affected (0.04 sec)

mysql> ALTER USER 'test'@'localhost' IDENTIFIED BY 'test-pass' WITH
    -> MAX_QUERIES_PER_HOUR 100
    -> PASSWORD EXPIRE INTERVAL 180 DAY
    -> FAILED_LOGIN_ATTEMPTS 3 PASSWORD_LOCK_TIME 3;
Query OK, 0 rows affected (0.04 sec)

mysql> GRANT SELECT ON test_db.orders TO 'test'@'localhost';
Query OK, 0 rows affected, 1 warning (0.02 sec)

mysql> SELECT * FROM INFORMATION_SCHEMA.USER_ATTRIBUTES WHERE USER='test';
+------+-----------+---------------------------------------+
| USER | HOST      | ATTRIBUTE                             |
+------+-----------+---------------------------------------+
| test | localhost | {"fname": "James", "lname": "Pretty"} |
+------+-----------+---------------------------------------+
1 row in set (0.00 sec)
```

## Задача 3
Установите профилирование SET profiling = 1. Изучите вывод профилирования команд SHOW PROFILES;.  
Исследуйте, какой engine используется в таблице БД test_db и приведите в ответе.  
Измените engine и приведите время выполнения и запрос на изменения из профайлера в ответе:  
- на MyISAM  
- на InnoDB  
```
mysql> SET profiling=1;
Query OK, 0 rows affected, 1 warning (0.00 sec)
mysql> SHOW PROFILES;
Empty set, 1 warning (0.00 sec)

mysql> SELECT TABLE_NAME,ENGINE,ROW_FORMAT,TABLE_ROWS,DATA_LENGTH,INDEX_LENGTH FROM information_schema.TABLES WHERE table_name='orders' and  TABLE_SCHEMA='test_db' ORDER BY ENGINE asc;
+------------+--------+------------+------------+-------------+--------------+
| TABLE_NAME | ENGINE | ROW_FORMAT | TABLE_ROWS | DATA_LENGTH | INDEX_LENGTH |
+------------+--------+------------+------------+-------------+--------------+
| orders     | InnoDB | Dynamic    |          5 |       16384 |            0 |
+------------+--------+------------+------------+-------------+--------------+
1 row in set (0.00 sec)

mysql> ALTER TABLE orders ENGINE = MyISAM;
Query OK, 5 rows affected (0.16 sec)
Records: 5  Duplicates: 0  Warnings: 0

mysql> ALTER TABLE orders ENGINE = InnoDB;
Query OK, 5 rows affected (0.21 sec)
Records: 5  Duplicates: 0  Warnings: 0

mysql> show profiles;
+----------+------------+-------------------------------+
| Query_ID | Duration   | Query                                                                                                                                                                            |
+----------+------------+-------------------------------+
|        1 | 0.00034750 | SELECT * FROM orders                                                                                                                                                             |
|        2 | 0.00340300 | SELECT * FROM information_schema.TABLES WHERE table_name='orders' and TABLE_SCHEMA='test_db' ORDER BY ENGINE asc                                                                 |
|        3 | 0.00145000 | SELECT TABLE_NAME,ENGINE,ROW_FORMAT,TABLE_ROWS,DATA_LENGTH,INDEX_LENGTH FROM information_schema.TABLES WHERE table_name='orders' and  TABLE_SCHEMA='test_db' ORDER BY ENGINE asc |
|        4 | 0.16153275 | ALTER TABLE orders ENGINE = MyISAM                                                                                                                                               |
|        5 | 0.21350625 | ALTER TABLE orders ENGINE = InnoDB                                                                                                                                               |
+----------+------------+-------------------------------+
5 rows in set, 1 warning (0.00 sec)
```

## Задача 4
Изучите файл my.cnf в директории /etc/mysql.  
Измените его согласно ТЗ (движок InnoDB):  
- Скорость IO важнее сохранности данных  
- Нужна компрессия таблиц для экономии места на диске  
- Размер буффера с незакомиченными транзакциями 1 Мб  
- Буффер кеширования 30% от ОЗУ  
- Размер файла логов операций 100 Мб  
Приведите в ответе измененный файл my.cnf.  
```

```
