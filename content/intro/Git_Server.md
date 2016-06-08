---
title: "Git Server"
layout: page
date: 2016-06-07 00:00
---

## 安装Linux服务器 ##

[安装LAMP环境](http://www.cnblogs.com/wenanry/archive/2012/11/13/2767779.html)
```
$ sudo apt-get install apache2 php5-mysql libapache2-mod-php5 mysql-server
```

### inux ###

linux版本为ubuntu 10 ,安装vmtools,

### ssh,git ###

安装ssh-server , git 
```
$ sudo apt-get install git-core
$ sudo apt-get install openssh-server openssh-client
``` 

### git server搭建 ###

[Git搭建服务器](http://developer.51cto.com/art/201507/483448.htm)
[Git搭建服务器-权限控制](http://www.linuxidc.com/Linux/2015-07/120616.htm)

[创建用户组](http://blog.csdn.net/poisonchry/article/details/11849781)再在用户组中添加git
[直接创建用户](http://www.liaoxuefeng.com/wiki/0013739516305929606dd18361248578c67b8067c8c017b000/00137583770360579bc4b458f044ce7afed3df579123eca000)

**自己实现方法 :**

创建用户git,并将工作目录指向/home/git
```
$ sudo adduser git -d /home/git
```

在/home/git目录下创建git仓库
```
$ sudo git init --bare warehouse.git
```

设置git用户密码
```
$ sudo passwd git
$ [passwd]
```

git 上传到服务器
```
$ git remote add origin git@192.168.0.212:/home/ninjia/warehouse.git
$ git push -u origin master
```
origin 为远程仓库名称,绑定git@192.168....
git push -u origin master 传输到origin的master分支

## 说明 ##

### 结构 ###

创建的warehouse.git ,在clone 后会存在warehouse文件夹,
clone warehouse.git后,如果再次clone会提示,已存在该文件夹warehouse
创建warehouse.git无法放到需要root权限文件夹,如果想放到root权限文件夹,必须把git用户加到root用户组中

git fetch 可以获取远程所有本地没有的数据
```
$ git fetch [remote-name]
```

[github远程仓库使用](https://git-scm.com/book/zh/ch2-5.html)

[github - book 使用](https://git-scm.com/book/zh/v2)

### 备注 ###

删除用户组
```
$ userdel username
$ groupdel username
$ usermod –G username username
```  
强制删除该用户的主目录和主目录下的所有文件和子目录

[linux用户组/用户 管理](http://www.cnblogs.com/xd502djj/archive/2011/11/23/2260094.html)