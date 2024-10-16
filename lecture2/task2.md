# Lecture 2

Isolation level for both sessions is `read committed`
```sh
postgres=# show transaction isolation level;
 transaction_isolation
-----------------------
 read committed
(1 row)
```

### Вопрос 1
Во второй сессии я вижу только 2 записи потому, что в первой сессии транзакция не завершена, а при уровне read committed мы не можем видеть незакомиченные записи.
```sh
postgres=*# SELECT * FROM persons;
 id | first_name | second_name
----+------------+-------------
  1 | dmitry     | dmitryev
  2 | anna       | ievleva
(2 rows)
```

### Вопрос 2
После коммита в первой сессии я вижу все записи во второй потому, что тразакция из первой сессии завершилась успешно и эти данные теперь доступны всем
```sh
postgres=*# SELECT * FROM persons;
 id | first_name | second_name
----+------------+-------------
  1 | dmitry     | dmitryev
  2 | anna       | ievleva
  3 | elena      | koroleva
(3 rows)
```
### Вопрос 3
При уровне изоляции repeatable read грязное чтение не допускается.
Поэтому начав в обоих сессия такие транзакции и добавив в первой запись в таблицу, я во второй ничего не увижу
```sh
postgres=*# SELECT * FROM persons;
 id | first_name | second_name
----+------------+-------------
  1 | dmitry     | dmitryev
  2 | anna       | ievleva
  3 | elena      | koroleva
(3 rows)
```

### Вопрос 4
После коммита в первой сессии я так же не увижу результ транзакции во второй, т.к. repeatable read позволяет видеть только те данные которые были перед началом транзакции
А вот после коммита во второй сесси уже 4 записи
```sh
postgres=# SELECT * FROM persons;
 id | first_name | second_name
----+------------+-------------
  1 | dmitry     | dmitryev
  2 | anna       | ievleva
  3 | elena      | koroleva
  4 | polina     | tsvetkova
(4 rows)
```
