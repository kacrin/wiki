---
title: "python2.7"
layout: page
date: 2017-02-09 23:00
---

# Python

## python2.7 安装升级

安装教材[http://www.cnblogs.com/balaamwe/p/3480430.html](http://www.cnblogs.com/balaamwe/p/3480430.html)

编译安装

Centos 6.4 python 2.6 升级到 2.7[http://blog.csdn.net/jcjc918/article/details/11022345](http://blog.csdn.net/jcjc918/article/details/11022345)

### install python2.7 && make install

download page [https://www.python.org/downloads/release/python-2713/](https://www.python.org/downloads/release/python-2713/)

```
# wget https://www.python.org/ftp/python/2.7.13/Python-2.7.13.tgz
# tar -zxvf Python-2.7.13.tgz
# cd Python-2.7.13
# ./configure
# make all
# make install
# make clean
# make distclean

# /usr/local/bin/python2.7 -V
# cd /usr/local/bin/:w
# mv python python2.6
# ln -s python2.7 python

# cd 
# python -V
```

解决python 软链到 python2.7 后，yum不兼容python2.7 ,制定yum 的python 版本

```
# vi /usr/bin/yum
```

修改头部

```
#!/usr/bin/python
```
为
```
#!/usr/bin/python2.6
```


### install pip setuptools

```
# wget https://bootstrap.pypa.io/get-pip.py
# python get-pip.py
# ln -s /usr/local/python2.7/bin/pip2.7 /usr/bin/pip
# pip -V
```

之前存在旧版本时先删除

```
rm -rf /usr/bin/pip
```