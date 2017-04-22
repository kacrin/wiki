---
title: "github_gh_pages"
layout: page
date: 2016-06-06 00:00
---

## Github ##

生成key在.ssh文件夹下,id_rsa为私钥不能泄漏，把id_rsa.pub文件内容，添加到Setting目录下[SSH and GPG keys](https://github.com/settings/keys)->New SSH key
- 可以添加多个key到Github上

### Introduction ###

使用[simiki](http://simiki.org)创建静态html页面,[github](http://www.github.com)发布wiki，上传源码，自己的[github](http://kacrin.github.io/wiki)链接，github可以托管开源项目，并且可以搭建静态个人网页，拥有二级域名

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
或者
```
pip install simiki
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

### 创建gh-pages分支

把静态页面添加到branch仓库，push到github仓库中
```
git branch gh-pages
git checkout gh-pages
git add *
git commmit -m "add gh-pages html "
git push origin master
```

在浏览中访问
[https://kacrin.github.io/wiki](http://kacrin.github.io/wiki)



