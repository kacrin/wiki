---
title: "Kali After"
layout: page
date: 2016-6-3 23:00
---

# KALI #

Kali 2016.2 Beat

[kali 秘籍](https://wizardforcel.gitbooks.io/kali-linux-cookbook/content/ch7.html)

## 更新kali源 ##

安装kali后添加源(/etc/apt/source.lst)
```python
deb http://mirrors.aliyun.com/kali kali main non-free contrib
deb-src http://mirrors.aliyun.com/kali kali main non-free contrib
deb http://mirrors.aliyun.com/kali-security kali/updates main contrib non-free
```
添加到source.lst后

##  安装中文输入法 ##

以下方法在local设为英文地区时无效

```
apt-get install ibus ibus-pinyin
```
```
apt-get install fcitx fcitx-googlepinyin
```

在设为为英文环境时使用fcitx中文输入法,在中文地区环境下可以，[ubuntu wiki fcix](http://wiki.ubuntu.com.cn/Fcitx)
系统使用gnome3,需要配置/usr/lib/x86_64-linux-gnu/gtk-3.0/3.0.0/immodules.cache,可以使终端使用中文输入法
在文件中，将
```
"xim" "X Input Method" "gtk20" "/usr/share/locale" "ko:ja:th:zh"
```
改为
```
"xim" "X Input Method" "gtk20" "/usr/share/locale" "en:ko:ja:th:zh"
```
在文件 /usr/lib/x86_64-linux-gnu/gtk-2.0/2.10.0/immodules.cache同样将文件
即可实现浏览器使用gtk-2.0程序，使用中文输入法
```
"xim" "X Input Method" "gtk20" "/usr/share/locale" "ko:ja:th:zh"
```
改为
```
"xim" "X Input Method" "gtk20" "/usr/share/locale" "en:ko:ja:th:zh"
```

重启计算机


## 学习simiki ##

学习[simiki](http://www.simiki.org)

* git使用
* markdown语法使用
* python编写框架

### 安装sublime ###

sublime无法使用中文输入

### 安装Lighttable ###

linux下强大开源代码编辑器，使用与sublime大致相同

```
$ tar -xzf LighttableLinux64.tar.gz
$ cd Lighttable
$ ./Lighttable
```
### 使用更强大编辑器 ###

emacs 
vim

### emacs

.emacs.d配置文件，把配置文件放到 ~/ 路径下

学习下emacs配置，放配置文件到git上一份



## Iceweasel ##

kali2016.2 brower is Friefox 

安装flash插件，[flash地址](https://get.adobe.com/flashplayer/)下载完后
```
$ tar -xzvf install_flash_player_11_linux.x86_64.tar.zip

$ cp libflashplayer.so /usr/lib/mozilla/plugins/
```


## rsync 文件复制工具 ##

rsync 可以显示复制文件进度

```
rsync -av --progress [Path] [ToPath]
```

## grub change image ##

grub image file 
```
/usr/share/image/grub
```


## php + apache2 + mysql 

kali 本身自带 php apache2 mysql

### apache2 配置

开启 rewrite_modules
配置子域名

### phpmyadmin 安装

安装phpmyadmin,并把phpmyadmin映射到/var/www/html/ 目录下

```
apt-get install phpmyadmin
ln -s /usr/share/phpmyadmin /var/www/html/
```

[mysql root 用户设置密码](www.cnblogs.com/panzulong/archive/2012/06/)
最简单快速设置mysql密码 
```
mysql>set password = password("mypasswd");
```
### 搭建开发环境

配置/etc/hosts 域名，配置apache2子域名

首先设置mysql密码，安装phpmyadmin，软链到/var/www/html/目录
使用phpmyadmin，导入数据库


## 安装QQ

[ubuntu wiki QQ 解决方案](http://wiki.ubuntu.com.cn/QQ)

使用[wine-qq 下载地址](http://www.longene.org/forum/viewtopic.php?f=6&t=30516),安装会缺少依赖条件，使用apt-get install -f

QQ wine32 can run

```
dpkg -i WineQQ7.8.deb
apt-get install -f
dpkg --add-architecture i386 && apt-get update && apt-get install wine32
```
安装依赖条件


## WPS

[WPS Download](http://wps-community.org/downloads?vl=a21#download)

install not find libpng12-0 [libpng12-0_1.2.54ubuntu1_amd64.deb download](http://packages.ubuntu.com/xenial/amd64/libpng12-0/download)

```
dpkg -i libpng12-0_1.2.54ubuntu1_amd64.deb
dpkg -i wps.deb
apt-get install wps.deb
dpkg -i wps.deb
```

## docker

```
apt-get install docker
```

## zsh  oh-my-zsh

```
apt-get install zsh

```
配置文件取arch zsh配置文件

## time 

[linux 查看修改时间地区](http://blog.163.com/zilichen0213@126/blog/static/17303755520118423614908/)

```
cp /usr/share/zoneinfo/Asia/Shanghai /etc/localtime
# 查看时间
date 
# 设定日期,格式 /月/日/年
date -s 8/11/16
# 设定时间
date -s 13:52:00
# 将当前时间和日期写入bios,避免重启失效
```

## scp 

```
scp /home/full.tar.gz root@172.19.2.75:/home/root
```

从远程下载到本地
```
scp root@172.19.2.75:/root/.bash_history /root/Desktop/bash_history
```
