---
title: 'Shortcut for excluding null and empty strings in MariaDB'
date: Fri, 03 Aug 2018 18:53:29 +0000
draft: false
tags: ['MariaDB', 'mariadb', 'sql-titbits']
---

Up until now when given the table below and working with MariaDB:```
CREATE TABLE t1 (id BIGINT NOT NULL AUTO\_INCREMENT, email VARCHAR(255) NULL, PRIMARY KEY(id));
INSERT INTO t1 (email) VALUES (''), ('     '), ('test@email.com'), (NULL);
```I would use the following `SELECT` query in order to eliminate both empty strings and `NULL` values from the result set:```
SELECT email FROM t1 WHERE email <> '' AND email IS NOT NULL;
```However, this can be shortened to the more succinct:```
SELECT email FROM t1 WHERE email > '';
```To explain, empty strings of any length are equal:```
MariaDB \[(none)\]> SELECT '' = '     ';
+--------------+
| '' = '     ' |
+--------------+
|            1 |
+--------------+
1 row in set (0.00 sec)
```So the expression in the `WHERE` clause would evaluate to false for all empty strings. Secondly, any comparison with `NULL` will return `NULL`.```
MariaDB \[(none)\]> SELECT NULL > '';
+-----------+
| NULL > '' |
+-----------+
|      NULL |
+-----------+
1 row in set (0.00 sec)
```For a row to be selected by the `WHERE` clause the expression should evaluate to true. Since `NULL` is not true the `NULL` values are also eliminated from the result set.