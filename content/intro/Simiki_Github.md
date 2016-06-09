---
title: "Simiki_Github"
layout: page
date: 2016-06-06 00:00
---

## Github ##

### Introduction ###

使用[simiki](http://simiki.org)创建静态html页面,[github](http://www.github.com)发布wiki，上传源码，自己的[github](http://kacrin.github.io/wiki)链接，github可以托管开源项目，并且可以搭建静态个人网页，拥有二级域名

### Github添加SSH_key ###

Liunx 终端下
```
$ ssh-keygen -t rsa -c "youemail@example.com"
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


### SSH Key ###

创建SSH Key。在用户主目录下，看看有没有.ssh目录，如果有，再看看这个目录下有没有id_rsa和id_rsa.pub这两个文件
```
$ ssh-keygen -t rsa -C 'youemail@example.com'
```
可以在ssh-keygen要求输入路径下重命名保存,默认id_rsa

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

查看分支
```
$ git branch

```
创建分支

```
$ git branch <name>
```
切换分支

```
$ git checkout <name>

```
创建+切换分支

```
$ git checkout -b <name>

```
合并某分支到当前分支

```
$ git merge <name>

```
删除分支

```
git branch -d <name>

```

## simiki自定义 ##

### Introduction ###

[simiki](http://simiki.org/zh-docs/)主題使用[Jinja2](http://jinja.pocoo.org/)一個強大模板引擎，由python开发
在content文件下存放源文件.md格式，使用[Markdown](http://www.jianshu.com/p/1e402922ee32/)语法编写，[马克飞象使用案例](https://maxiang.io)

### simiki基本命令 ###

simiki安装首先要安装pip
```
$ pip install simiki
```
直接下载[simiki](https://github.com/tankywoo/simiki)
```
$ unzip simiki.zip
$ cd simiki
$ python setup.py install
```


安装好Simiki后, 就可以使用simiki命令


```
# 查看 simiki 命令帮助文档
$ simiki -h

# 生成静态页面到output目录 (需要在站点根目录下执行)
$ simiki g

# 生成静态页面(包括草稿)到output目录 (需要在站点根目录下执行)
$ simiki g --draft

# 本地预览模式(开发模式)
$ simiki p

# 本地预览模式指定绑定IP和端口, 如绑定到所有IP的8888端口
$ simiki p --host 0.0.0.0 --port 8888

# 本地预览模式监控content目录, 有变更自动更新生成相应静态页面
$ simiki p -w

# 升级Simiki后检查和更新本地内置的脚本及主题(如fabfile.py, simple主题)
$ simiki update

```




