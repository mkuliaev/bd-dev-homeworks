# Домашнее задание к занятию 2. «SQL»

## Задача 1

Используя Docker, поднимите инстанс PostgreSQL (версию 12) c 2 volume, 
в который будут складываться данные БД и бэкапы.

Приведите получившуюся команду или docker-compose-манифест.

```
version: '3.8'

services:
  db:
    image: postgres:12
    container_name: postgres12
    environment:
      POSTGRES_USER: kuliaev
      POSTGRES_PASSWORD: password
      POSTGRES_DB: kuliaev-db
    volumes:
      - db_data:/var/lib/postgresql/data
      - /home/kuliaev/backups:/backups

volumes:
  db_data:


```

## Задача 2

В БД из задачи 1: 

- создайте пользователя test-admin-user и БД test_db;
- в БД test_db создайте таблицу orders и clients (спeцификация таблиц ниже);
- предоставьте привилегии на все операции пользователю test-admin-user на таблицы БД test_db;
- создайте пользователя test-simple-user;
- предоставьте пользователю test-simple-user права на SELECT/INSERT/UPDATE/DELETE этих таблиц БД test_db.

Таблица orders:

- id (serial primary key);
- наименование (string);
- цена (integer).

Таблица clients:

- id (serial primary key);
- фамилия (string);
- страна проживания (string, index);
- заказ (foreign key orders).

Приведите:

- итоговый список БД после выполнения пунктов выше;
- описание таблиц (describe);
- SQL-запрос для выдачи списка пользователей с правами над таблицами test_db;
- список пользователей с правами над таблицами test_db.

```
test_db=# \l
                                    List of databases
    Name    |      Owner      | Encoding |  Collate   |   Ctype    |  Access privileges  
------------+-----------------+----------+------------+------------+---------------------
 kuliaev-db | kuliaev         | UTF8     | en_US.utf8 | en_US.utf8 | 
 postgres   | kuliaev         | UTF8     | en_US.utf8 | en_US.utf8 | 
 template0  | kuliaev         | UTF8     | en_US.utf8 | en_US.utf8 | =c/kuliaev         +
            |                 |          |            |            | kuliaev=CTc/kuliaev
 template1  | kuliaev         | UTF8     | en_US.utf8 | en_US.utf8 | =c/kuliaev         +
            |                 |          |            |            | kuliaev=CTc/kuliaev
 test_db    | test-admin-user | UTF8     | en_US.utf8 | en_US.utf8 | 
(5 rows)

test_db=# \d orders
                                       Table "public.orders"
    Column    |          Type          | Collation | Nullable |              Default               
--------------+------------------------+-----------+----------+------------------------------------
 id           | integer                |           | not null | nextval('orders_id_seq'::regclass)
 наименование | character varying(255) |           |          | 
 цена         | integer                |           |          | 
Indexes:
    "orders_pkey" PRIMARY KEY, btree (id)
Referenced by:
    TABLE "clients" CONSTRAINT "clients_заказ_fkey" FOREIGN KEY ("заказ") REFERENCES orders(id)

test_db=# \d clients
                                         Table "public.clients"
      Column       |          Type          | Collation | Nullable |               Default               
-------------------+------------------------+-----------+----------+-------------------------------------
 id                | integer                |           | not null | nextval('clients_id_seq'::regclass)
 фамилия           | character varying(255) |           |          | 
 страна_проживания | character varying(255) |           |          | 
 заказ             | integer                |           |          | 
Indexes:
    "clients_pkey" PRIMARY KEY, btree (id)
    "idx_clients_country" btree ("страна_проживания")
Foreign-key constraints:
    "clients_заказ_fkey" FOREIGN KEY ("заказ") REFERENCES orders(id)



```

```
test_db=> SELECT grantee, table_catalog, table_schema, table_name, privilege_type
FROM information_schema.role_table_grants
WHERE table_catalog = 'test_db';
     grantee     | table_catalog | table_schema | table_name | privilege_type 
-----------------+---------------+--------------+------------+----------------
 test_admin_user | test_db       | public       | orders     | INSERT
 test_admin_user | test_db       | public       | orders     | SELECT
 test_admin_user | test_db       | public       | orders     | UPDATE
 test_admin_user | test_db       | public       | orders     | DELETE
 test_admin_user | test_db       | public       | orders     | TRUNCATE
 test_admin_user | test_db       | public       | orders     | REFERENCES
 test_admin_user | test_db       | public       | orders     | TRIGGER
 test_admin_user | test_db       | public       | clients    | INSERT
 test_admin_user | test_db       | public       | clients    | SELECT
 test_admin_user | test_db       | public       | clients    | UPDATE
 test_admin_user | test_db       | public       | clients    | DELETE
 test_admin_user | test_db       | public       | clients    | TRUNCATE
 test_admin_user | test_db       | public       | clients    | REFERENCES
 test_admin_user | test_db       | public       | clients    | TRIGGER
(14 rows)

test_db=> 
```



## Задача 3

Используя SQL-синтаксис, наполните таблицы следующими тестовыми данными:

Таблица orders

|Наименование|цена|
|------------|----|
|Шоколад| 10 |
|Принтер| 3000 |
|Книга| 500 |
|Монитор| 7000|
|Гитара| 4000|

Таблица clients

|ФИО|Страна проживания|
|------------|----|
|Иванов Иван Иванович| USA |
|Петров Петр Петрович| Canada |
|Иоганн Себастьян Бах| Japan |
|Ронни Джеймс Дио| Russia|
|Ritchie Blackmore| Russia|

Используя SQL-синтаксис:
- вычислите количество записей для каждой таблицы.

Приведите в ответе:

    - запросы,
    - результаты их выполнения.


```
test_db=# INSERT INTO orders (наименование, цена) VALUES
('Шоколад', 10),
('Принтер', 3000),
('Книга', 500),
('Монитор', 7000),
('Гитара', 4000);
INSERT 0 5

```

```
test_db=# INSERT INTO clients (фамилия, страна_проживания) VALUES
('Иванов Иван Иванович', 'USA'),
('Петров Петр Петрович', 'Canada'),
('Иоганн Себастьян Бах', 'Japan'),
('Ронни Джеймс Дио', 'Russia'),
('Ritchie Blackmore', 'Russia');
INSERT 0 5


```

```
test_db=# SELECT COUNT(*) AS Количество_записей_orders FROM orders;
 Количество_записей_orders 
---------------------------
                         5
(1 row)

test_db=# SELECT COUNT(*) AS Количество_записей_clients FROM clients;
 Количество_записей_clients 
----------------------------
                          5
(1 row)

test_db=# SELECT COUNT(*) AS order_count FROM orders;
 order_count 
-------------
           5
(1 row)

test_db=# SELECT COUNT(*) AS client_count FROM clients;
 client_count 
--------------
            5
(1 row)


```


## Задача 4

Часть пользователей из таблицы clients решили оформить заказы из таблицы orders.

Используя foreign keys, свяжите записи из таблиц, согласно таблице:

|ФИО|Заказ|
|------------|----|
|Иванов Иван Иванович| Книга |
|Петров Петр Петрович| Монитор |
|Иоганн Себастьян Бах| Гитара |

Приведите SQL-запросы для выполнения этих операций.

Приведите SQL-запрос для выдачи всех пользователей, которые совершили заказ, а также вывод этого запроса.
 
Подсказка: используйте директиву `UPDATE`.

## Задача 5

Получите полную информацию по выполнению запроса выдачи всех пользователей из задачи 4 
(используя директиву EXPLAIN).

Приведите получившийся результат и объясните, что значат полученные значения.

## Задача 6

Создайте бэкап БД test_db и поместите его в volume, предназначенный для бэкапов (см. задачу 1).

Остановите контейнер с PostgreSQL, но не удаляйте volumes.

Поднимите новый пустой контейнер с PostgreSQL.

Восстановите БД test_db в новом контейнере.

Приведите список операций, который вы применяли для бэкапа данных и восстановления. 

---


