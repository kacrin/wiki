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

```python
apt-get install ibus ibus-pinyin
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
