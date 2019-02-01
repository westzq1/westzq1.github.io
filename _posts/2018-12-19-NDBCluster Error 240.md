---
layout: post
title: NDBCluster - Error 240
modified: 2018-12-19
categories: [MySQL, NDBCluster]
tags: 
  - MySQL.NDBCluster
comments: true
---
MySQL NDBCluster Error 240

Yesterday, an error 240 was reported in our MySQL NDBCluster database which is an very old NDBCluster, version is mysql-5.6.25 ndb-7.4.7
<pre class="prettyprint lang-sql linenums=1 ">mysql> START TRANSACTION;
Query OK, 0 rows affected (0.00 sec)

mysql> Delete from table1 where uid=29 and rid in (2,4,5,6,7,8);
Query OK, 6 rows affected (0.00 sec)

mysql> INSERT INTO table1 (uid, rid) VALUES (29, 5);
Query OK, 1 row affected (0.01 sec)

mysql> INSERT INTO table1 (uid, rid) VALUES (29, 4);
ERROR 1296 (HY000): Got error 240 'Unknown error code' from NDBCLUSTER
</pre>

Error Code information can be found here

https://dev.mysql.com/doc/ndbapi/en/ndb-error-codes-internal-error.html

Error 240 means “Invalid data encountered during foreign key trigger execution”

I told the coder to rerun the SQL, but the error occurred again.

I did some testing on my own.
table1 has two FK, one is to table_u and another is to table_r.
If we do not start transaction, and use autocommit=on, there is no error

<pre class="prettyprint lang-sql linenums=1 ">mysql> Delete from table1 where uid=29 and rid in (2,4,5,6,7,8);
Query OK, 6 rows affected (0.01 sec)

mysql> INSERT INTO table1 (uid, rid) VALUES (29, 5);
Query OK, 1 row affected (0.01 sec)

mysql> INSERT INTO table1 (uid, rid) VALUES (29, 4);
Query OK, 1 row affected (0.00 sec)

mysql> INSERT INTO table1 (uid, rid) VALUES (29, 8);
Query OK, 1 row affected (0.00 sec)

mysql> INSERT INTO table1 (uid, rid) VALUES (29, 2);
Query OK, 1 row affected (0.01 sec)

mysql> INSERT INTO table1 (uid, rid) VALUES (29, 7);
Query OK, 1 row affected (0.00 sec)

mysql> INSERT INTO table1 (uid, rid) VALUES (29, 6);
Query OK, 1 row affected (0.00 sec)

mysql> commit;
Query OK, 0 rows affected (0.00 sec)
</pre>
And if we disable the check of foreign key in session no error.
<pre class="prettyprint lang-sql linenums=1 ">mysql> set session foreign_key_checks=0;
Query OK, 0 rows affected (0.00 sec)

mysql> START TRANSACTION;
Query OK, 0 rows affected (0.00 sec)

mysql> Delete from table1 where uid=29 and rid in (2,4,5,6,7,8);
Query OK, 6 rows affected (0.00 sec)

mysql> INSERT INTO table1 (uid, rid) VALUES (29, 5);

Query OK, 1 row affected (0.00 sec)

mysql> INSERT INTO table1 (uid, rid) VALUES (29, 4);
Query OK, 1 row affected (0.01 sec)

mysql> INSERT INTO table1 (uid, rid) VALUES (29, 8);
Query OK, 1 row affected (0.00 sec)

mysql> INSERT INTO table1 (uid, rid) VALUES (29, 2);
Query OK, 1 row affected (0.00 sec)

mysql> INSERT INTO table1 (uid, rid) VALUES (29, 7);

Query OK, 1 row affected (0.00 sec)

mysql> INSERT INTO table1 (uid, rid) VALUES (29, 6);
Query OK, 1 row affected (0.01 sec)

mysql> commit;
Query OK, 0 rows affected (0.00 sec)

mysql> set session foreign_key_checks=1;
Query OK, 0 rows affected (0.00 sec)
</pre>
I tried to rolling restart the NDB data node, unfortunately, the error is still there.
I copied the related table to an new database on the same instance.
<pre class="prettyprint lang-sql linenums=1 ">mysqldump -uroot -pxxxx --skip-add-drop-table portal table1 table2 table3 |mysql -uroot -pxxxx portal1
</pre>
And then, do test, there is no error.

So, we copied all database to the new database, the error disappeared.

There are some article online about this error, but no one fits to our situation.

MySQL NDBCluster is the only engine in MySQL that supports FK. It uses trigger internal to achieve its goal. 

So, Error 240 means that an error encounters during the FK checking.

Multiple reasons may cause this error, for example, there is no enough memory.

There is no way to know the reason. But I guess there is a logical corruption on the index.
I suggest to try the following, if the application can stop:
1. Recreate PKs which the FK relay on.
2. Recreate FKs on all related tables.
3. Shutdown the whole cluster and restart, in the way, indexes will be recreated.
4. Backup / Drop / Restore all related tables.
