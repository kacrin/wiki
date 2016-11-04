---
title: "Centos Sever"
layout: page
date: 2016-11-03 23:00
---


# gitlab 

## 搭建教程

[gitlab中文网-Centos docker gitlab](https://bbs.gitlab.cc/topic/39/%E5%9C%A8-centos-7-%E4%B8%8A%E4%BD%BF%E7%94%A8-docker-%E9%83%A8%E7%BD%B2%E5%AE%89%E8%A3%85-gitlab/2)
 
```
centos7  ip : 139.**.**.198   name:root   passwod: ???
```
 
此命令会指向docker80端口到外部80端口  保存文件及配置文件到/data/gitlab/目录

```
docker run --detach --hostname git.taxkiz.com --publish 80:80 --name gitlab-8.0.5 --restart always --volume /data/gitlab/config:/etc/gitlab --volume /data/gitlab/logs:/var/log/gitlab --volume /data/gitlab/data:/var/opt/gitlab gitlab/gitlab-ce:8.0.5-ce.0
```

## 默认用户名密码

用户: root
密码: 5iveL!fe

设置容器80端口哦映射外部端口88

```
docker run --detach --hostname git.taxkiz.com --publish 88:80 --name gitlab-8.0.5 --restart always --volume /data/gitlab/config:/etc/gitlab --volume /data/gitlab/logs:/var/log/gitlab --volume /data/gitlab/data:/var/opt/gitlab gitlab/gitlab-ce:8.0.5-ce.0
```

git传输会使用22端口，docker中没有映射外部22端口，无法push/pull，ssh连接也会使用22连接端口，首先修改外部ssh连接端口，再把22端口映射到docker容器中
```
docker run --detach --hostname 139.224.27.198 --publish 88:80 --publish 22:22 --name gitlab-8.0.5_88_ip_ssh22 --restart always --volume /data/gitlab/config:/etc/gitlab --volume /data/gitlab/logs:/var/log/gitlab --volume /data/gitlab/data:/var/opt/gitlab gitlab/gitlab-ce:8.0.5-ce.0
```

## docker 

[docker](http://wiki.jikexueyuan.com/list/docker/)


# 环境搭建

```
Linux: centos7
Apache:  httpd-2.4.18
PHP:  php-5.6.25
MySQL: mysql-5.6.33
Memcached: memcached-1.4.31
SVN: Subversion-1.7.14
FTP: vsftpd-3.0.2
```

## lamp 架构

[centos 搭建lamp架构](https://www.howtoforge.com/apache_php_mysql_on_centos_7_lamp)
[centos 搭建lamp架构2](http://www.jb51.net/os/188488.html)


mysql 安装 
```
yum -y install mysql mariadb-server
```

mysql启动

```
systemctl start mariadb  #启动MariaDB
systemctl stop mariadb  #停止MariaDB
systemctl restart mariadb  #重启MariaDB
systemctl enable mariadb  #设置开机启动
```

数据库出错，忘掉密码 ，删除再按安装

```
yum remove mysql mysql-server mysql-libs compat-mysql51
rm -rf /var/lib/mysql
rm /etc/my.cnf
查看是否还有mysql软件
rpm -qa|grep mysql
有的话继续删除
```

设置sql密码

```
mysql -u root 
mysql> use mysql
mysql> SET PASSWORD FOR 'root'@'localhost' = PASSWORD('newpwd');
```

## vsftpd 

安装及配置
