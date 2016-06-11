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

**注意用户权限问题**非root用户，普通用户权限把仓库建在需要root权限目录下需要修改上一级目录用户归属
```
$ chown git:git <filename>
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

## Git Server自动化部署 ##

### git勾子 ###

Git可以定制一些钩子，这些钩子可以在特定的情况下被执行，分为Client端的钩子和Server端的钩子
[git hooks](http://blog.csdn.net/hongchangfirst/article/details/46693237)分为ClientSide hooks 和 ServerSide hooks

[官方git hooks](https://git-scm.com/book/en/v2/Customizing-Git-Git-Hooks)介绍，hooks文件夹下各个文件被调用勾子说明

### 自动化部署 ###

**需要在/var/www/下创建warehouse文件夹，并在/var/www/warehouse目录下执行**
```
$ git init
$ git remote add origin /home/ninjia/warehouse.git
```
把远程仓库地址命名为origin，方便下次直接调用


修改hooks文件夹下post-receive.sample文件去掉.sample
使用bash脚本，修改post-receive如下
```
#!/bin/sh
export LANG=zh_CN.UTF-8
cd /var/www/warehouse
unset GIT_DIR
git pull origin master
```
使用git后，push文件后需要git pull到服务器上才可以更新
git有勾子设置，在hooks文件夹内，调用勾子时shell脚本可以执行，但是git pull时会报错，提示"fatal:Not a git repository;".原因是执行cd后，继续执行git语句拉取到时候还是在hooks文件夹下，不是cd到文件路径

**添加unset GIT_DIR**
git的hooks里面默认有一些环境变量，会导致无论在哪个语句之后执行git命令都会有一个默认的环境路径，既然这样unset 掉默认的GIT环境变量就可以了

**一定要设置warehouse.git文件夹下权限和/var/www/warehouse/.git/文件夹下所有文件权限**
在普通用户组非root的权限下，需要设置文件归属
设置warehouse.git和.git所有文件归属为git:git用户

```
$ cd /home/ninjia/warehouse.git
$ chown git:git * -R
$ cd /var/www/warehouse/.git
$ chown git:git * -R

```


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
