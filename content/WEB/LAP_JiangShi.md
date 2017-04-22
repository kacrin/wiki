---
title: "LAP_Jiangshi"
layout: page
date: 2016-06-15 00:00
---

## 安装Linux服务器 ##


[安装LAMP环境](http://www.cnblogs.com/wenanry/archive/2012/11/13/2767779.html),安装  vsftpd  ssh

```
$ sudo apt-get install apache2 php5-mysql libapache2-mod-php5 mysql-server
$ sudo apt-get install openssh-server openssh-client
$ sudo apt-get install vsftpd
```

修改vsftpd不能上传,/在etc/vsftpd.conf中将“write_enable=YES”前面的#取消。

```
$ sudo vi /etc/vsftpd.conf
# del 'write_enable=YES' before '#'
$ sudo /etc/init.d/vsftpd restart
```


## 更改ubuntu web server ##


### 开启apache2地址重写模块 ###

ubuntu下apache跟其他linux稍有不同,其他linux配置另google
因为Ubuntu下，Apache的配置文件及目录结构不大一样，所以mods-avalilable下的是可用的模块，而真正启用了的模块是在mods-enable目录底下的。所以自己创建软连接也可以实现启用rewrite模块[链接地址](http://www.2cto.com/os/201307/227899.html)

```
$ sudo a2enmod rewrite 
$ sudo ln -s /etc/apache2/mods-available/rewrite.load /etc/apache2/mods-enabled/rewrite.load  
```

还要修改/etc/apache2/sites-available/default这个文件

```
<Directory /> 
Options FollowSymLinks 
AllowOverride None（修改为AllowOverride All） 
</Directory> 
<Directory "/var/orioner"> 
Options Indexes FollowSymLinks MultiViews 
AllowOverride None（修改为AllowOverride All） 
Order allow,deny 
allow from all 
</Directory>
```

重启apache2

```
$ sudo /etc/init.d/apache2 restart 
```

修改mysql配置文件,把数据库默认编码全部改为UTF-8,默认配置文件存放在/etc/my.cnf或者/etc/mysql/my.cnf[链接地址](http://www.liaoxuefeng.com/wiki/001374738125095c955c1e6d8bb493182103fac9270762a000/001391435131816c6a377e100ec4d43b3fc9145f3bb8056000)

```
[client]
default-character-set = utf8

[mysqld]
default-storage-engine = INNODB
character-set-server = utf8
collation-server = utf8_general_ci
```

重启MySQL后，可以通过MySQL的客户端命令行检查编码：

```
$ mysql -u root -p
Enter password: 
Welcome to the MySQL monitor

mysql> show variables like '%char%';
```


## 上传网站源码 ##


### 上传前修改好ThinkPHP配置文件 ###

上传前检查所有配置文件config.php,根目录下  config.inic.php  index.php  common.php  中域名,域名检查,及二级域名设置,以及继承  common.php 中调用的域名检查
重新配置config.php下路由大小写问题

```
'SITE_DOMAIN'       => 'http://zxwh.xxx.net',  // 主网址
```

### 利用git仓库钩子自动部署(Pull)到网站目录 ###

部署查看[kacrin.github.io/wiki](http://kacrin.github.io),上传
```
$ git add .htaccess 
$ git add *
$ git commit -m "upload web source on server"
$ git remote add origin git@192.168.0.212:/home/ninjia/web.git
$ git push -u origin master
```

### 配置服务器域名 ###

域名配置

[http://www.cnblogs.com/zhangyabin---acm/p/4358510.html](http://www.cnblogs.com/zhangyabin---acm/p/4358510.html)
[http://www.cnblogs.com/ylan2009/archive/2012/02/25/2368028.html](http://www.cnblogs.com/ylan2009/archive/2012/02/25/2368028.html)

```
<VirtualHost *:80>
        ServerName zxwh.xxxx.cn
        DocumentRoot /var/www/zxwh
        ErrorLog /var/log/apache2/error-zxwh.xxxx.log
</VirtualHost>
```

配置端口监听

```
Listen 12551
NameVirtualHost *:12551
<VirtualHost *:12551>

        DocumentRoot "E:\BanGong\zentaopms\www"
        <Directory "E:\BanGong\zentaopms\www">
                AllowOverride All
                Allow from all
        </Directory>
</VirtualHost>
```

### 导入数据库 ###

[数据库备份:http://www.cnblogs.com/kissdodog/p/4174421.html](http://www.cnblogs.com/kissdodog/p/4174421.html)

```
#备份出数据库内容,mysqldump基本语法：
$ mysqldump -u username -p dbname table1 table2 ...-> BackupName.sql
#使用root用户备份test数据库下的person表
$ mysqldump -u root -p test person > D:\backup.sql
#备份多个数据库
mysqldump -u username -p --databases dbname2 dbname2 > Backup.sql
#mysqldump命令备份所有数据库的语法如下：
mysqldump -u username -p -all-databases > BackupName.sql
```

导入数据,source后面要跟绝对路径

```
$ mysql -u root -p 
# input your mysql password
$ create database [database]
$ use [database]
$ source /home/ninjia/database.sql
```


## 备注 ##


测试服务器,出现500错误,可能是重写错误,先把.htacess文件重命名,看主页是否打开,再开启dbug模式,调试出现错误,一般网站还会有缓存,要首先创建这几个文件夹,并给予755,写入权限
