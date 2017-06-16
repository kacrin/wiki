---
title: "MySql Master-slave synchronization"
layout: page
date: 2017-06-15 10:00
---


# Master-slave synchronization

[mysql主从复制实现数据库同步](http://www.cnblogs.com/rwxwsblog/p/4542417.html)

[MySQL数据库主从同步配置](http://www.wanghualang.com/mysql-master-slave.html)

[MySQL数据的主从复制、半同步复制和主主复制详解](http://blog.csdn.net/goustzhu/article/details/9339621)

[MySql主从同步总结](https://www.mawenbao.com/note/mysql-replication-summary.html)

## master database server

### 配置conf

配置 /etc/my.conf 

```
[mysqld]
server-id = 1
log-bin = mysql-bin
binlog-do-db = database
binlog-ignore-db = mysql,information_schema
```

server-id 数据库ID，此ID为唯一，主库默认为1，其他从库在此ID递增即可，不能重复

log-bin = mysql-bin 二进制日志文件，此项为必填项

binlog-do-db 需同步的数据库，需同步多个数据库，继续添加此项

```
binlog-do-db= database1
```

保存完后，重启mysqld

### 创建一个同步用户

```
mysql> grant replication slave on *.* to 'slave'@'%' identified by 'mypassword';
```

% 为所有IP都可连接，可设置为唯一IP

查看主数据库状态,得到binlog日志文件和偏移量，后面配置从数据库时需要

```
mysql> show master status;
+------------------+----------+--------------+------------------+
| File             | Position | Binlog_Do_DB | Binlog_Ignore_DB |
+------------------+----------+--------------+------------------+
| mysql-bin.000003 |      245 |              |                  |
+------------------+----------+--------------+------------------+
1 row in set (0.00 sec)
```

### 备份数据库

设置读锁,防止备份时写入数据

```
mysql> flush tables with read lock;
```

备份

```
> mysqldump test > test.sql
```

解锁

```
mysql> unlock tables;
```


## slave database server

配置 my.conf
```
server-id = 23
```

数据库配置,此处要注意logfile的名称和position的值，其余host、user和password为主数据库设置的账号和密码

```
mysql> stop slave;
Query OK, 0 rows affected (0.00 sec)

mysql> change master to
　　 -> master_host='192.168.0.107',
　　 -> master_user='slave',
　　 -> master_password='mypassword',
　　 -> master_log_file='mysql-bin.000003',
　　 -> master_log_pos=245;

mysql> start slave;
Query OK, 0 rows affected (0.00 sec)

mysql> show slave status\G;
*************************** 1. row ***************************
Slave_IO_State: Waiting for master to send event
Master_Host: 192.168.0.107
Master_User: repl
Master_Port: 3306
Connect_Retry: 60
Master_Log_File: mysql-bin.000001
Read_Master_Log_Pos: 1079
Relay_Log_File: mysqld-relay-bin.000004
Relay_Log_Pos: 251
Relay_Master_Log_File: mysql-bin.000001
Slave_IO_Running: Yes
Slave_SQL_Running: Yes
Replicate_Do_DB:
Replicate_Ignore_DB:
Replicate_Do_Table:
Replicate_Ignore_Table:
Replicate_Wild_Do_Table:
Replicate_Wild_Ignore_Table:
Last_Errno: 0
Last_Error:
Skip_Counter: 0
Exec_Master_Log_Pos: 1079
Relay_Log_Space: 407
Until_Condition: None
Until_Log_File:
Until_Log_Pos: 0
Master_SSL_Allowed: No
Master_SSL_CA_File:
Master_SSL_CA_Path:
Master_SSL_Cert:
Master_SSL_Cipher:
Master_SSL_Key:
Seconds_Behind_Master: 0
Master_SSL_Verify_Server_Cert: No
Last_IO_Errno: 0
Last_IO_Error:
Last_SQL_Errno: 0
Last_SQL_Error:
1 row in set (0.00 sec)
ERROR:
No query specified
```

在这里主要是看:

```
Slave_IO_Running=Yes
Slave_SQL_Running=Yes
```

如果出现Slave_IO_Running: No或Slave_SQL_Running: NO，

重做,数据库配置操作
