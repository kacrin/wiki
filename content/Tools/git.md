---
title: "Git Server"
layout: page
date: 2016-06-07 00:00
---

## git


### Github添加SSH_key ###

Liunx 终端下
```
$ ssh-keygen -t rsa -C "youemail@example.com"
$ cd .ssh
$ cat id_rsa.pub
```
生成key在.ssh文件夹下,id_rsa为私钥不能泄漏，把id_rsa.pub文件内容，添加到Setting目录下[SSH and GPG keys](https://github.com/settings/keys)->New SSH key
- 可以添加多个key到Github上

### Git ###

Make dir warehouse
```
$ mkdir learngit
$ cd learngit
$ pwd
/Users/michael/learngit
```

use the git
```
$ git init
$ git add readme.md
$ git commit -m "wrote a readme file"
```

git status命令可以让我们时刻掌握仓库当前的状态

```
$ git status
# On branch master
# Changes not staged for commit:
#   (use "git add <file>..." to update what will be committed)
#   (use "git checkout -- <file>..." to discard changes in working directory)
#
#    modified:   readme.txt
#
no changes added to commit (use "git add" and/or "git commit -a")
```

git diff查看difference

```
$ git diff readme.txt
diff --git a/readme.txt b/readme.txt
index 46d49bf..9247db6 100644
--- a/readme.txt
+++ b/readme.txt
@@ -1,2 +1,2 @@
-Git is a version control system.
+Git is a distributed version control system.
 Git is free software.

```

rules before

```
$ git commit -m "upload a file"
```

[git](http://www.liaoxuefeng.com/wiki/0013739516305929606dd18361248578c67b8067c8c017b000/0013743858312764dca7ad6d0754f76aa562e3789478044000)

git status Everytime look warehouse_code

HEAD指向的版本就是当前版本，因此，Git允许我们在版本的历史之间穿梭，使用命令
```
$ git reset --hard commit_id
```
穿梭前，用git log可以查看提交历史，以便确定要回退到哪个版本。
要返未来，用git reflog查看命令历史，以便确定要回到未来的哪个版本

### Add Remote ###

GitHub告诉我们，可以从这个仓库克隆出新的仓库，也可以把一个已有的本地仓库与之关联，然后，把本地仓库的内容推送到GitHub仓库
```
$ git remote add origin git@github.com:michaelliao/learngit.git
```

远程库的名字就是origin，这是Git默认的叫法，也可以改成别的，但是origin这个名字一看就知道是远程库

把本地master分支的最新修改推送至GitHub
```
$ git push -u origin master
```

此后，每次本地提交后，只要有必要，就可以使用命令,推送最新修改
```
git push origin master
```

### git branch ###

```
查看分支
$ git branch

创建分支
$ git branch <name>

切换分支
$ git checkout <name>

创建+切换分支
$ git checkout -b <name>

合并某分支到当前分支
$ git merge <name>

删除分支
git branch -d <name>

```

回撤单个文件,实现方法
```
$ git checkout <commit-hash> <name-file>
```
回撤的文件成已从修改commit 状态,可直接checkout -f <name-file> 撤回

## git install

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
定义输出路径/www/warehouse 强制输出覆盖到此路径
```
#!/bin/sh
git --work-tree=/www/warehouse checkout -f
```

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
