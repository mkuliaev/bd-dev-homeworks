# Домашнее задание к занятию 4. «PostgreSQL»

## Задача 1

Используя Docker, поднимите инстанс PostgreSQL (версию 13). Данные БД сохраните в volume.

Подключитесь к БД PostgreSQL, используя `psql`.

```  docker exec -it postgres13 bash  ```


```  psql -U postgres  ```

Воспользуйтесь командой `\?` для вывода подсказки по имеющимся в `psql` управляющим командам.

**Найдите и приведите** управляющие команды для:

- вывода списка БД,

```  \l  ```


- подключения к БД,


``` \c имя бызы данных ```
- вывода списка таблиц,

``` \dt ```
- вывода описания содержимого таблиц,


```\d имя таблици ```
- выхода из psql.

``` \q ```



## Задача 2

Используя `psql`, создайте БД `test_database`.

Изучите [бэкап БД](https://github.com/netology-code/virt-homeworks/tree/virt-11/06-db-04-postgresql/test_data).

Восстановите бэкап БД в `test_database`.

Перейдите в управляющую консоль `psql` внутри контейнера.

Подключитесь к восстановленной БД и проведите операцию ANALYZE для сбора статистики по таблице.

Используя таблицу [pg_stats](https://postgrespro.ru/docs/postgresql/12/view-pg-stats), найдите столбец таблицы `orders` 
с наибольшим средним значением размера элементов в байтах.

**Приведите в ответе** команду, которую вы использовали для вычисления, и полученный результат.

```
kuliaev@postgresql:~$ docker exec -it postgres13 bash
root@455f6eeb6d7e:/# psql -U postgres
psql (13.15 (Debian 13.15-1.pgdg120+1))
Type "help" for help.

postgres=# CREATE DATABASE test_database;
CREATE DATABASE

```


```
kuliaev@postgresql:~$ docker cp /home/kuliaev/test_dump.sql postgres13:/test_dump.sql
Successfully copied 4.1kB to postgres13:/test_dump.sql
kuliaev@postgresql:~$ docker exec -it postgres13 bash
root@455f6eeb6d7e:/# psql -U postgres -d test_database -f /test_dump.sql
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
root@455f6eeb6d7e:/# psql -U postgres -d test_database
psql (13.15 (Debian 13.15-1.pgdg120+1))
Type "help" for help.

test_database=# ANALYZE;
ANALYZE
test_database=# SELECT attname, avg_width
   FROM pg_stats
   WHERE tablename = 'orders'
   ORDER BY avg_width DESC
   LIMIT 1;
 attname | avg_width 
---------+-----------
 title   |        16
(1 row)

test_database=#
```

## Задача 3

Архитектор и администратор БД выяснили, что ваша таблица orders разрослась до невиданных размеров и
поиск по ней занимает долгое время. Вам как успешному выпускнику курсов DevOps в Нетологии предложили
провести разбиение таблицы на 2: шардировать на orders_1 - price>499 и orders_2 - price<=499.

Предложите SQL-транзакцию для проведения этой операции.

```
test_database=# BEGIN;
BEGIN
test_database=*# CREATE TABLE orders_1 AS SELECT * FROM orders WHERE price > 499;
SELECT 3
test_database=*# CREATE TABLE orders_2 AS SELECT * FROM orders WHERE price <= 499;
SELECT 5
test_database=*# 
test_database=*# DROP TABLE orders;
DROP TABLE
test_database=*# ALTER TABLE orders_1 RENAME TO orders_price_1_499;
ALTER TABLE
test_database=*# ALTER TABLE orders_2 RENAME TO orders_price_2_499;
ALTER TABLE
test_database=*# COMMIT;
COMMIT
test_database-# \q

```
Можно ли было изначально исключить ручное разбиение при проектировании таблицы orders? 
`В PostgreSQL можно создать партиционированную таблицу`.


## Задача 4

Используя утилиту `pg_dump`, создайте бекап БД `test_database`.

Как бы вы доработали бэкап-файл, чтобы добавить уникальность значения столбца `title` для таблиц `test_database`?

```

```


```

```