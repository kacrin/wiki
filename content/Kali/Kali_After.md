---
title: "Kali After"
layout: page
date: 2016-6-3 23:00
---

# KALI #

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

```
apt-get install ibus ibus-pinyin
```

```
apt-get install fcitx fcitx-googlepinyin
```

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

```
dpkg -i WineQQ7.8.deb
apt-get install -f
```
安装依赖条件

## docker

```
apt-get install docker
```

## zsh  oh-my-zsh

```
apt-get install zsh
```
配置文件取arch zsh配置文件