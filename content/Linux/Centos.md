---
title: "Centos"
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

[最好的vsftpd配置](http://www.cnblogs.com/hhuai/archive/2011/02/12/1952647.html)

```
# Allow anonymous FTP? (Beware - allowed by default if you comment this out).
#anonymous_enable=YES
anonymous_enable=NO
设定不允许匿名访问
#
# Uncomment this to allow local users to log in.
local_enable=YES
设定本地用户可以访问。注意：主要是为虚拟宿主用户，如果该项目设定为NO那么所有虚拟用户将无法访问。
#
# Uncomment this to enable any form of FTP write command.
write_enable=YES
设定可以进行写操作。
#
# Default umask for local users is 077. You may wish to change this to 022,
# if your users expect that (022 is used by most other ftpd's)
local_umask=022
设定上传后文件的权限掩码。
#
# Uncomment this to allow the anonymous FTP user to upload files. This only
# has an effect if the above global write enable is activated. Also, you will
# obviously need to create a directory writable by the FTP user.
#anon_upload_enable=YES
anon_upload_enable=NO
禁止匿名用户上传。
#
# Uncomment this if you want the anonymous FTP user to be able to create
# new directories.
#anon_mkdir_write_enable=YES
anon_mkdir_write_enable=NO
禁止匿名用户建立目录。
#
# Activate directory messages - messages given to remote users when they
# go into a certain directory.
dirmessage_enable=YES
设定开启目录标语功能。
#
# Activate logging of uploads/downloads.
xferlog_enable=YES
设定开启日志记录功能。
#
# Make sure PORT transfer connections originate from port 20 (ftp-data).
connect_from_port_20=YES
设定端口20进行数据连接。
#
# If you want, you can arrange for uploaded anonymous files to be owned by
# a different user. Note! Using "root" for uploaded files is not
# recommended!
#chown_uploads=YES
chown_uploads=NO
设定禁止上传文件更改宿主。
#chown_username=whoever
#
# You may override where the log file goes if you like. The default is shown
# below.
xferlog_file=/var/log/vsftpd.log
设定Vsftpd的服务日志保存路径。注意，该文件默认不存在。必须要手动touch出来，并且由于这里更改了Vsftpd的服务宿主用户为手动建立的Vsftpd。必须注意给与该用户对日志的写入权限，否则服务将启动失败。
#
# If you want, you can have your log file in standard ftpd xferlog format
xferlog_std_format=YES
设定日志使用标准的记录格式。
#
# You may change the default value for timing out an idle session.
#idle_session_timeout=600
设定空闲连接超时时间，这里使用默认。将具体数值留给每个具体用户具体指定，当然如果不指定的话，还是使用这里的默认值600，单位秒。
#
# You may change the default value for timing out a data connection.
#data_connection_timeout=120
设定单次最大连续传输时间，这里使用默认。将具体数值留给每个具体用户具体指定，当然如果不指定的话，还是使用这里的默认值120，单位秒。
#
# It is recommended that you define on your system a unique user which the
# ftp server can use as a totally isolated and unprivileged user.
#nopriv_user=ftpsecure
nopriv_user=vsftpd
设定支撑Vsftpd服务的宿主用户为手动建立的Vsftpd用户。注意，一旦做出更改宿主用户后，必须注意一起与该服务相关的读写文件的读写赋权问题。比如日志文件就必须给与该用户写入权限等。
#
# Enable this and the server will recognise asynchronous ABOR requests. Not
# recommended for security (the code is non-trivial). Not enabling it,
# however, may confuse older FTP clients.
async_abor_enable=YES
设定支持异步传输功能。
#
# By default the server will pretend to allow ASCII mode but in fact ignore
# the request. Turn on the below options to have the server actually do ASCII
# mangling on files when in ASCII mode.
# Beware that on some FTP servers, ASCII support allows a denial of service
# attack (DoS) via the command "SIZE /big/file" in ASCII mode. vsftpd
# predicted this attack and has always been safe, reporting the size of the
# raw file.
# ASCII mangling is a horrible feature of the protocol.
ascii_upload_enable=YES
ascii_download_enable=YES
设定支持ASCII模式的上传和下载功能。
#
# You may fully customise the login banner string:
ftpd_banner=This Vsftp server supports virtual users ^_^
设定Vsftpd的登陆标语。
#
# You may specify a file of disallowed anonymous e-mail addresses. Apparently
# useful for combatting certain DoS attacks.
#deny_email_enable=YES
# (default follows)
#banned_email_file=/etc/vsftpd/banned_emails
#
# You may specify an explicit list of local users to chroot() to their home
# directory. If chroot_local_user is YES, then this list becomes a list of
# users to NOT chroot().
#chroot_list_enable=YES
chroot_list_enable=NO
禁止用户登出自己的FTP主目录。
# (default follows)
#chroot_list_file=/etc/vsftpd/chroot_list
#
# You may activate the "-R" option to the builtin ls. This is disabled by
# default to avoid remote users being able to cause excessive I/O on large
# sites. However, some broken FTP clients such as "ncftp" and "mirror" assume
# the presence of the "-R" option, so there is a strong case for enabling it.
#ls_recurse_enable=YES
ls_recurse_enable=NO
禁止用户登陆FTP后使用"ls -R"的命令。该命令会对服务器性能造成巨大开销。如果该项被允许，那么挡多用户同时使用该命令时将会对该服务器造成威胁。
# When "listen" directive is enabled, vsftpd runs in standalone mode and
# listens on IPv4 sockets. This directive cannot be used in conjunction
# with the listen_ipv6 directive.
listen=YES
设定该Vsftpd服务工作在StandAlone模式下。顺便展开说明一下，所谓StandAlone模式就是该服务拥有自己的守护进程支持，在ps -A命令下我们将可用看到vsftpd的守护进程名。如果不想工作在StandAlone模式下，则可以选择SuperDaemon模式，在该模式下 vsftpd将没有自己的守护进程，而是由超级守护进程Xinetd全权代理，与此同时，Vsftp服务的许多功能将得不到实现。
#
# This directive enables listening on IPv6 sockets. To listen on IPv4 and IPv6
# sockets, you must run two copies of vsftpd whith two configuration files.
# Make sure, that one of the listen options is commented !!
#listen_ipv6=YESpam_service_name=vsftpd
设定PAM服务下Vsftpd的验证配置文件名。因此，PAM验证将参考/etc/pam.d/下的vsftpd文件配置。
userlist_enable=YES
设定userlist_file中的用户将不得使用FTP。
tcp_wrappers=YES
设定支持TCP Wrappers。#KC: The following entries are added for supporting virtual ftp users.
以下这些是关于Vsftpd虚拟用户支持的重要配置项目。默认Vsftpd.conf中不包含这些设定项目，需要自己手动添加配置。guest_enable=YES
设定启用虚拟用户功能。
guest_username=overlord
指定虚拟用户的宿主用户。
virtual_use_local_privs=YES
设定虚拟用户的权限符合他们的宿主用户。
user_config_dir=/etc/vsftpd/vconf
设定虚拟用户个人Vsftp的配置文件存放路径。也就是说，这个被指定的目录里，将存放每个Vsftp虚拟用户个性的配置文件，一个需要注意的地方就是这些配置文件名必须和虚拟用户名相同。
```

虚拟用户没有实现，使用vsftpd登陆

使用vsftpd账号登陆vsftpd
设置vsftpd密码
```
# useradd vsftpd -s /sbin/nologin
# passwd vsftpd
```

## phpmyadmin 

[phpmyadmin安装配置](https://linux.cn/article-4526-1.html)

centos6,7中配置方法不同

centos7 中安装 配置外网访问，默认外网不可访问

```
# yum -y install phpmyadmin php-mcrypt
# vi /etc/httpd/conf.d/phpMyAdmin.conf
```

找出并注释掉带有"Require ip xxx" 字样代码行，用"Require all granted" 代之

```

<Directory /usr/share/phpMyAdmin/>
   AddDefaultCharset UTF-8
 
   <IfModule mod_authz_core.c>
     # Apache 2.4
     <RequireAny>
       #Require ip 127.0.0.1
       #Require ip ::1
       Require all granted
     </RequireAny>
   </IfModule>
   <IfModule !mod_authz_core.c>
     # Apache 2.2
     Order Deny,Allow
     Deny from All
     Allow from 127.0.0.1
     Allow from ::1
   </IfModule>
</Directory>
 
<Directory /usr/share/phpMyAdmin/setup/>
   <IfModule mod_authz_core.c>
     # Apache 2.4
     <RequireAny>
       #Require ip 127.0.0.1
       #Require ip ::1
       Require all granted
     </RequireAny>
   </IfModule>
   <IfModule !mod_authz_core.c>
     # Apache 2.2
     Order Deny,Allow
     Deny from All
     Allow from 127.0.0.1
     Allow from ::1
   </IfModule>
</Directory>

```

重启使之生效

```
# systemctl restart httpd
```

centos 6 中配置，查看网址中centos6中配置

# git 实现完美钩子

使用gitlab完美实现？

使用git工具直接实现git远程仓库,写钩子，每次提交完成后，强制检查不同哦你并更新到/www/pms 目录
```
git init --base pms.git
cd pms.git/hooks
vi post-receive

#!/bin/bash
git --work-tree=/www/pms checkout -f

```



# service

```
sshd服务
openssh-server

scp ssh 服务
openssh-client


psmisc
Psmisc软件包包含三个帮助管理/proc目录的程序。
安装下列程序: fuser, killall,pstree和pstree.x11(到pstree的链接)
fuser 显示使用指定文件或者文件系统的进程的PID。
killall 杀死某个名字的进程，它向运行指定命令的所有进程发出信号。
pstree 树型显示当前运行的进程。
pstree.x11 与pstree功能相同，只是在退出前需要确认。


yum install net-tools
网络命令工具
```