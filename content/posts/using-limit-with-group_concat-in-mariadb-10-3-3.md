---
title: 'Using LIMIT with GROUP_CONCAT  in MariaDB 10.3.3+'
date: Fri, 05 Oct 2018 17:17:28 +0000
draft: false
tags: ['group-concat', 'MariaDB', 'mariadb', 'sql-titbits']
---

Prior to version 10.3.3 of MariaDB it was not possible to use `LIMIT` in a `GROUP_CONCAT` query. So in cases you wanted to select the first n elements of a `GROUP_CONCAT` query you had to make use of `SUBSTRING_INDEX`.```
CREATE TABLE t1 (id BIGINT NOT NULL AUTO\_INCREMENT, email VARCHAR(255), PRIMARY KEY(id));
INSERT INTO t1 (email) VALUES ('email'), ('test2@email.com'), ('test1@email.com');

```To select the first element you would execute the following query:```
SELECT SUBSTRING\_INDEX(GROUP\_CONCAT(email SEPARATOR '::'), '::', _n_) FROM t1; 

_where n is the number of elements required_ 
```This has always felt hacky to me. Now you can make the query simple and straightforward by using:```
SELECT GROUP\_CONCAT(email SEPARATOR '::' LIMIT _n_) FROM t1; 

```