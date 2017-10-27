---
title: "Kali After"
layout: page

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
首先设置 Region & language 中 Formats 为中国(汉语)

然后安装 ibus 和 ibus-pinyin
```
apt-get install ibus ibus-pinyin
```
安装完ibus 后，在Input Sources 中，添加 Chinese 拼音,
必须在input source 中添加Chinese(pinyin),才可以使用fcitx时使用中文
删除ibus 输入法

```
apt-get install fcitx fcitx-googlepinyin
apt-get remove ibus
```

在设为为英文环境时使用fcitx中文输入法,在中文地区环境下可以，[ubuntu wiki fcix](http://wiki.ubuntu.com.cn/Fcitx)
系统使用gnome3,需要配置/usr/lib/x86_64-linux-gnu/gtk-3.0/3.0.0/immodules.cache,可以使终端使用中文输入法
在文件中，将
```
"xim" "X Input Method" "gtk30" "/usr/share/locale" "ko:ja:th:zh"
```
改为
```
"xim" "X Input Method" "gtk30" "/usr/share/locale" "en:ko:ja:th:zh"
```

```
"fcitx" "Fcitx (Free Chinese Input Toy of X)" "fcitx" "/usr/share/locale" "ja:ko:zh:*" 
```
改为
```
"fcitx" "Fcitx (Free Chinese Input Toy of X)" "fcitx" "/usr/share/locale" "en:ja:ko:zh:*" 
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

```
"fcitx" "Fcitx (Free Chinese Input Toy of X)" "fcitx" "/usr/share/locale" "ja:ko:zh:*" 
```
改为
```
"fcitx" "Fcitx (Free Chinese Input Toy of X)" "fcitx" "/usr/share/locale" "en:ja:ko:zh:*" 
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

在init.el 中添加,设置默认的中文字体，解决不在终端下打开，中文字体问题 
```
(set-fontset-font "fontset-default" 'han '("Microsoft YaHei". "unicode-bmp"))

```



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

### apache 重写

Linux开启url重写的方法：

打开 apache 里httpd.conf（通常是在/etc/httpd/conf目录里）

找到 #LoadModule rewrite_module modules/mod_rewrite.so 去掉前面的#

找到 AllowOverride None 改成 AllowOverride All , 有两个全部改掉

注：AllowOverride 的参数设置为ALL，表示整台服务器上都支持URL规则重写。Apache 服务器要读每个网站下目录下的 .htaccess 文件。如果没有这个文件，或者这个文档没有定义任何关于URL重写的规则就不会有任何效果。

重启apache (用命令：service httpd restart)。


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

## apache2配置子域名

/etc/apache2/sites-enabled/000-default.conf

```
<VirtualHost *:80>
	ServerAdmin localhost
	DocumentRoot /var/www/html
	ServerName localhost
	ErrorLog ${APACHE_LOG_DIR}/error.log
	# CustomLog ${APACHE_LOG_DIR}/access.log combined
</VirtualHost>
			 
```


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
hwclock -w
```

## scp 

```
scp /home/full.tar.gz root@172.19.2.75:/home/root
```

从远程下载到本地
```
scp root@172.19.2.75:/root/.bash_history /root/Desktop/bash_history
```

## smb

smb

```
#列出某个ip所提供共享文件夹
smbclient -L 192.168.3.145 -U username%password
#像ftp客户端一样使用smbclient
smbclient //192.168.3.145/项目 -U username%password
#kali/centos中使用cifs代替smbfs,映射远程地址到本地
mount.cifs //192.168.3.145/项目 ~/home/share -o username=root,password=pw
#or 
mount -t cifs //192.168.3.145/项目 ~/root/share -o username=root,password=pw
```

如果存在smbmount
```
smbmount //192.168.3.145/项目 ~/home/share -o username=root,password=pw
#or 
mount -t smbfs //192.168.3.145/项目 ~/root/share -o username=root,password=pw
```

smbclient连接批量拉取文件
```
smbclient -Udomainname/fordodone //10.234.92.21/sharename
Password:
Domain=[DOMAINNAME] OS=[Windows 5.0] Server=[Windows 2000 LAN Manager]
smb: \> cd testdir
smb: \testdir\> get C
NT_STATUS_FILE_IS_A_DIRECTORY opening remote file \testdir\C
smb: \testdir\> prompt
smb: \testdir\> recurse
smb: \testdir\> mget C
getting file ...

```

## tmux

没有显示256color解决方案
在～/.bashrc 中添加 alias tmux='tmux -2',然后是配置生效#source ~./bashrc
在~/.tmux.conf中添加 set -g default-terminal "screen-256color"


## expect

expect 实现自动登录ftp帐号和ssh连接

[expect 使用教程](http://blog.csdn.net/zhuying_linux/article/details/6902135)

```
#!/usr/bin/expect
set timeout 30
spawn ssh -l username 192.168.1.1
expect "password:"
send "ispass/r"
interact
```

1. ［#!/usr/bin/expect］
这一行告诉操作系统脚本里的代码使用那一个shell来执行。这里的expect其实和Linux下的bash、windows下的cmd是一类东西。
注意：这一行需要在脚本的第一行。
2. ［set timeout 30］
基本上认识英文的都知道这是设置超时时间的，现在你只要记住他的计时单位是：秒
3. ［spawn ssh -l username 192.168.1.1］
spawn是进入expect环境后才可以执行的expect内部命令，如果没有装expect或者直接在默认的SHELL下执行是找不到spawn命令的。所以不要用 “which spawn“之类的命令去找spawn命令。好比windows里的dir就是一个内部命令，这个命令由shell自带，你无法找到一个dir.com 或 dir.exe 的可执行文件。
它主要的功能是给ssh运行进程加个壳，用来传递交互指令。
4. ［expect "password:"］
这里的expect也是expect的一个内部命令，有点晕吧，expect的shell命令和内部命令是一样的，但不是一个功能，习惯就好了。这个命令的意思是判断上次输出结果里是否包含“password:”的字符串，如果有则立即返回，否则就等待一段时间后返回，这里等待时长就是前面设置的30秒
5. ［send "ispass\r"］
这里就是执行交互动作，与手工输入密码的动作等效。
温馨提示： 命令字符串结尾别忘记加上“\r”，如果出现异常等待的状态可以核查一下。 
6. ［interact］
执行完成后保持交互状态，把控制权交给控制台，这个时候就可以手工操作了。如果没有这一句登录完成后会退出，而不是留在远程终端上。如果你只是登录过去执行 
