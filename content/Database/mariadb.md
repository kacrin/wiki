---
title: "mariadb"
layout: page
date: 2017-04-28 15:00
---


# Intro 

mariadb 官方中文文档[https://mariadb.com/kb/zh-cn/mariadb/](https://mariadb.com/kb/zh-cn/mariadb/)

## add user

```
[root@localhost]# mysql
Welcome to the MariaDB monitor. Commands end with ; or \g.
Your MariaDB connection id is 7
Server version: 5.6.41-MariaDB MariaDB Server

Copyright (c) 2000, 2014, Oracle, MariaDB Corporation Ab and others.

Type 'help;' or '\h' for help. Type '\c' to clear the current input statement.

MariaDB [(none)]> use mysql
Reading table information for completion of table and column names
You can turn off this feature to get a quicker startup with -A

MariaDB [(none)]> insert into mysql.user(Host,User,Password) values("localhost","admin",password("admin"));;
Query OK, 1 rows affected (0.00 sec)

MariaDB [(none)]> GRANT ALL PRIVILEGES ON *.* TO 'admin@'%'IDENTIFIED BY 'admin' WITH GRANT OPTION;
Query OK, 0 rows affected (0.00 sec)

MariaDB [mysql]> flush privileges;
Query OK, 0 rows affected (0.00 sec)


MariaDB [mysql]> exit
```

## set password

```
[root@localhost etc]# mysql
Welcome to the MariaDB monitor. Commands end with ; or \g.
Your MariaDB connection id is 7
Server version: 5.6.41-MariaDB MariaDB Server

Copyright (c) 2000, 2014, Oracle, MariaDB Corporation Ab and others.

Type 'help;' or '\h' for help. Type '\c' to clear the current input statement.

MariaDB [(none)]> use mysql
Reading table information for completion of table and column names
You can turn off this feature to get a quicker startup with -A

MariaDB [mysql]> update user set password=password("123456")where user='root';
Query OK, 0 rows affected (0.00 sec)
Rows matched: 4 Changed: 0 Warnings: 0

MariaDB [mysql]> flush privileges;
Query OK, 0 rows affected (0.00 sec)

MariaDB [mysql]> exit
```


