---
title: "Docker"
layout: page
date: 2016-11-17 23:00
---

# Intro

Docker — 从入门到实践[https://yeasy.gitbooks.io/docker_practice/content/](https://yeasy.gitbooks.io/docker_practice/content/)

Docker — w3school[https://www.w3cschool.cn/docker](https://www.w3cschool.cn/docker)

Docker — DaoCloud[http://guide.daocloud.io/dcs/daocloud-9153151.html](http://guide.daocloud.io/dcs/daocloud-9153151.html)

Docker — RUNOOB[http://www.runoob.com/docker/docker-tutorial.html](http://www.runoob.com/docker/docker-tutorial.html)

Docker — 极客学院[http://wiki.jikexueyuan.com/project/docker/](http://wiki.jikexueyuan.com/project/docker/)

Docker 中文指南 [http://www.widuu.com/docker/index.html](http://www.widuu.com/docker/index.html)

Docker 官方文档 [https://docs.docker.com/engine/installation](https://docs.docker.com/engine/installation)

## DaoCloud

[DaoCloud](https://www.daocloud.io/mirror#accelerator-doc) 提供 pull 加速 永久使用

# Install

[Docker安装 官方文档](https://docs.docker.com/engine/installation/)

## Centos

### Centos7

```
yum -y install docker
```

## Debian

Debian中分为 Jessie or Stretch 和 Wheezy

使用dpkg安装Wheezy版本,在[安装包地址](https://download.docker.com/linux/debian/dists/wheezy/pool/stable/)选择版本 下载

```
dpkg -i package.deb
```

# config


## 修改保存地址

### 修改配置文件
查看使用帮助

```
# docker daemon --help
-g, --graph=/var/lib/docker：Root of the Docker runtime
```

修改docker配置文件

在 Ubuntu/Debian 系统下:
编辑 /etc/default/docker 文件, 添加-g 参数的设置, 如下:

```
DOCKER_OPTS="-dns 8.8.8.8 -dns 8.8.4.4 -g /mnt"
```

在 Fedora/Centos 系统下:

编辑 /etc/sysconfig/docker 文件, 添加-g 参数的设置, 如下:

```
other_args="-g /mnt"
```

重启服务

```
# /etc/init.d/docker restart
```

查看信息

```
# docker info
Containers: 16
Images: 66
Server Version: 1.9.1
Storage Driver: aufs
 Root Dir: /docker/aufs
 Backing Filesystem: extfs
 Dirs: 98
 Dirperm1 Supported: true
Execution Driver: native-0.2
Logging Driver: json-file
Kernel Version: 3.16.0-0.bpo.4-amd64
Operating System: Debian GNU/Linux 7 (wheezy)
CPUs: 32
Total Memory: 94.54 GiB
Name: uy01-03
ID: AYRJ:AIXS:47NX:LURW:KOM6:AVVM:CUAF:QFXW:2QDP:ONNP:RHKJ:LG37
```

### 使用链接

1) 停止 Docker: service docker stop.
2) 做个备份 tar -zcC /var/lib/docker > /mnt/var_lib_docker-backup-$(date + %s).tar.gz
3) 迁移/var/lib/docker目录到met 目录下: mv /var/lib/docker /mnt/docker
4) 建个 symlink: ln -s /mnt/docker /var/lib/docker
5) 确认文件夹类型为symlink 类型 ls /var/lib/docker
6) 启动 docker service.

# docker 镜像

查找镜像
```
# docker search <search-data>
```

拉取镜像
```
# docker pull <centos>
```

查看镜像
```
# docker images
```

删除镜像
```
# docker rmi <container-id>
```

导出镜像
```
# docker save <repository:tag> > /home/image_name.tar
```

导入镜像
```
# docker load < /home/image_name.tar
```

# docker 容器

新建并启动容器使用 docker run 

使用容器输出hello world 然后终止容器

```
# docker run ubuntu:14.04 /bin/echo 'hello world'
```

启用一个bash终端，交互运行

```
# docker run -t -d -i -p 8000:80 ubuntu:14.04 /bin/bash
```
-t:在新容器内指定一个伪终端或终端。
-i:允许你对容器内的标准输入 (STDIN) 进行交互。
-d:后台运行
-p:映射端口 映射本地8000端口到docker80端口
-v:卷映射

查看所有容器（正在运行，已终止）
```
# docker ps -a
```

停止容器
```
# docker stop <container-id>/<names>
```

使已终止容器运行
```
# docker start <names>
```

查看正在运行的容器
```
# docker ps 
```

删除容器
```
# docker rm <container-id>/<names>
```

容器导出
```
# docker export 0291b910e062 > debian-2015_11_15.tar
```

容器导入
```
# docker import xxx????
```

容器提交为镜像
```
# docker commit -m="commit explain" -a="作者名称" <container-id> name1/name2:v1
```


#docker 仓库

