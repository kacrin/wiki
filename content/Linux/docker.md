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

## dockerfile

Dockerfile的指令是忽略大小写的，建议使用大写，使用#作为注释，每一行只支持一条指令，每条指令可以携带多个参数。

Dockerfile的指令根据作用可以分为两种：构建指令和设置指令。构建指令用于构建image，其指定的操作不会在运行image的容器上执行；设置指令用于设置image的属性，其指定的操作将在运行image的容器中执行。

1）FROM（指定基础image）

构建指令，必须指定且需要在Dockerfile其他指令的前面。后续的指令都依赖于该指令指定的image。FROM指令指定的基础image可以是官方远程仓库中的，也可以位于本地仓库。

该指令有两种格式：
```	
FROM <image>
```

指定基础image为该image的最后修改的版本。或者：
```
FROM <image>:<tag>
```

指定基础image为该image的一个tag版本。

2）MAINTAINER（用来指定镜像创建者信息）

构建指令，用于将image的制作者相关的信息写入到image中。当我们对该image执行docker inspect命令时，输出中有相应的字段记录该信息。格式：
```	
MAINTAINER <name>
```

3）RUN（执行命令）

构建指令，RUN可以运行任何被基础image支持的命令。如基础image选择了centos，那么软件管理部分只能使用centos的命令。该指令有两种格式：
```
RUN <command> (the command is run in a shell - `/bin/sh -c`)
RUN ["executable", "param1", "param2" ... ] (exec form)
```

4）CMD（设置容器启动时执行的操作）

设置指令，用于container启动时指定的操作。该操作可以是执行自定义脚本，也可以是执行系统命令。该指令只能在文件中存在一次，如果有多个，则只执行最后一条。该指令有三种格式：
```
CMD ["executable","param1","param2"] (like an exec, this is the preferred form)
CMD command param1 param2 (as a shell)
```

CMD主要用于container时启动指定的服务，当Docker run command的命令匹配到CMD command时，会替换CMD执行的命令。

当Dockerfile指定了ENTRYPOINT，那么使用下面的格式：
```	
CMD ["param1","param2"] (as default parameters to ENTRYPOINT)
```

ENTRYPOINT指定的是一个可执行的脚本或者程序的路径，该指定的脚本或者程序将会以param1和param2作为参数执行。所以如果CMD指令使用上面的形式，那么Dockerfile中必须要有配套的ENTRYPOINT。

5）ENTRYPOINT（设置容器启动时执行的操作）

container启动时执行的命令，但是一个Dockerfile中只能有一条ENTRYPOINT命令，如果多条，则只执行最后一条。ENTRYPOINT没有CMD的可替换特性。两种格式：
```
ENTRYPOINT ["executable", "param1", "param2"] (like an exec, the preferred form)
ENTRYPOINT command param1 param2 (as a shell)
```

该指令的使用分为两种情况，一种是独自使用，另一种和CMD指令配合使用。

当独自使用时，如果你还使用了CMD命令且CMD是一个完整的可执行的命令，那么CMD指令和ENTRYPOINT会互相覆盖只有最后一个CMD或者ENTRYPOINT有效。
# CMD指令将不会被执行,只有ENTRYPOINT指令被执行;
```
CMD echo "Hello, World!"
ENTRYPOINT ls -l
```

另一种用法和CMD指令配合使用来指定ENTRYPOINT的默认参数，这时CMD指令不是一个完整的可执行命令，仅仅是参数部分；ENTRYPOINT指令只能使用JSON方式指定执行命令，而不能指定参数。
```
FROM ubuntu
CMD ["-l"]
ENTRYPOINT ["/usr/bin/ls"]
```

6）USER（设置容器的用户）

设置指令，设置启动容器的用户，默认是root用户。
	
指定memcached的运行用户;
```
ENTRYPOINT ["memcached"]
USER daemon

ENTRYPOINT ["memcached", "-u", "daemon"]
```

7）EXPOSE（指定容器需要映射到宿主机器的端口）

设置指令，该指令会将容器中的端口映射成宿主机器中的某个端口。当你需要访问容器的时候，可以不是用容器的IP地址而是使用宿主机器的IP地址和映射后的端口。要完成整个操作需要两个步骤，首先在Dockerfile使用EXPOSE设置需要映射的容器端口，然后在运行容器的时候指定-p选项加上EXPOSE设置的端口，这样EXPOSE设置的端口号会被随机映射成宿主机器中的一个端口号。也可以指定需要映射到宿主机器的那个端口，这时要确保宿主机器上的端口号没有被使用。EXPOSE指令可以一次设置多个端口号，相应的运行容器的时候，可以配套的多次使用-p选项。

格式:
```
EXPOSE <port> [<port>...]
	
映射一个端口;
EXPOSE port1
 
相应的运行容器使用的命令;
docker run -p port1 image
 
映射多个端口;
EXPOSE port1 port2 port3
 
相应的运行容器使用的命令;
docker run -p port1 -p port2 -p port3 image
 
还可以指定需要映射到宿主机器上的某个端口号;
docker run -p host_port1:port1 -p host_port2:port2 -p host_port3:port3 image
```

端口映射是docker比较重要的一个功能，原因在于我们每次运行容器的时候容器的IP地址不能指定而是在桥接网卡的地址范围内随机生成的。宿主机器的IP地址是固定的，我们可以将容器的端口的映射到宿主机器上的一个端口，免去每次访问容器中的某个服务时都要查看容器的IP的地址。对于一个运行的容器，可以使用docker port加上容器中需要映射的端口和容器的ID来查看该端口号在宿主机器上的映射端口。

8）ENV（用于设置环境变量）

构建指令，在image中设置一个环境变量。格式:
```	
ENV <key> <value>
```

设置了后，后续的RUN命令都可以使用，container启动后，可以通过docker inspect查看这个环境变量，也可以通过在docker run –env key=value时设置或修改环境变量。

假如你安装了JAVA程序，需要设置JAVA_HOME，那么可以在Dockerfile中这样写：
```	
ENV JAVA_HOME /path/to/java/dirent
```

9）ADD（从src复制文件到container的dest路径）

构建指令，所有拷贝到container中的文件和文件夹权限为0755，uid和gid为0；如果是一个目录，那么会将该目录下的所有文件添加到container中，不包括目录；如果文件是可识别的压缩格式，则docker会帮忙解压缩（注意压缩格式）；如果<src>是文件且<dest>中不使用斜杠结束，则会将<dest>视为文件，<src>的内容会写入<dest>；如果<src>是文件且<dest>中使用斜杠结束，则会<src>文件拷贝到<dest>目录下。
格式:
	
```
ADD <src> <dest>
```

<src>：是相对被构建的源目录的相对路径，可以是文件或目录的路径，也可以是一个远程的文件url。

<dest>：是container中的绝对路径。

10）VOLUME（指定挂载点）

设置指令，使容器中的一个目录具有持久化存储数据的功能，该目录可以被容器本身使用，也可以共享给其他容器使用。我们知道容器使用的是AUFS，这种文件系统不能持久化数据，当容器关闭后，所有的更改都会丢失。当容器中的应用有持久化数据的需求时可以在Dockerfile中使用该指令。格式:
	
```
VOLUME ["&lt;mountpoint&gt;"]

FROM base
VOLUME ["/tmp/data"]
```

运行通过该Dockerfile生成image的容器，/tmp/data目录中的数据在容器关闭后，里面的数据还存在。例如另一个容器也有持久化数据的需求，且想使用上面容器共享的/tmp/data目录，那么可以运行下面的命令启动一个容器：
```
$ docker run -t -i -rm -volumes-from container1 image2 bash
```

container1为第一个容器的ID，image2为第二个容器运行image的名字。

11）WORKDIR（切换目录）

设置指令，可以多次切换(相当于cd命令)，对RUN,CMD,ENTRYPOINT生效。格式:
```
WORKDIR /path/to/workdir
```

在/p1/p2下执行vim a.txt;
```
WORKDIR /p1 WORKDIR p2 RUN vim a.txt
```

12）ONBUILD（在子镜像中执行）
```
ONBUILD <Dockerfile关键字>
```

ONBUILD指定的命令在构建镜像时并不执行，而是在它的子镜像中执行。
二、创建Dockerfile文件

创建一个构建nginx镜像的Dockerfile文件，Git地址：[https://github.com/dongwenpeng/nginx](https://github.com/dongwenpeng/nginx)
```
$ cat dockerfile

FROM nginx 
MAINTAINER dkey
ENV RUN_USER nginx 
ENV RUN_GROUP nginx 
ENV DATA_DIR /data/web
ENV LOG_DIR /data/log/nginx
RUN mkdir /data/log/nginx -p 
RUN chown nginx.nginx -R /data/log/nginx
ADD web /data/web
ADD nginx.conf /etc/nginx/nginx.conf
ADD default.conf /etc/nginx/conf.d/default.conf
EXPOSE 80
ENTRYPOINT nginx -g "daemon off;"
```

做了这么几件事：

1、拉取一个nginx镜像。

2、设置了几个变量。

3、创建了几个需要的目录。

4、把当前目录下的web程序复制到镜像的/data/web目录。

5、把nginx.conf配置文件和default.conf配置文件复制到镜像中。

6、设置一个默认端口。

7、最后设置了容器启动时执行的命令，我用来启动nginx程序，注意这个命令不能错，不然容器启动不了。这样设置后，当你docker run运行此镜像时不需要在后面再次执行需要执行的命令了。
三、构建nginx镜像

脚本写好了，需要转换成镜像（跟dockerfile在同一个目录）：

```
$ docker build -t nginx_01 .

Sending build context to Docker daemon  3.43 MB
Step 1 : FROM nginx
latest: Pulling from library/nginx

64f0219ba3ea: Pull complete 
325b624bee1c: Pull complete 
Digest: sha256:2a07a07e5bbf62e7b583cbb5257357c7e0ba1a8e9650e8fa76d999a60968530f
Status: Downloaded newer image for nginx:latest
 ---> 19146d5729dc
Step 2 : MAINTAINER dkey
 ---> Running in a9d12e4fa972
 ---> 3e6bcc394986
Removing intermediate container a9d12e4fa972
Step 3 : ENV RUN_USER nginx
 ---> Running in 55e165c28124
 ---> 024ba866269e
Removing intermediate container 55e165c28124
Step 4 : ENV RUN_GROUP nginx
 ---> Running in 577cb4556709
 ---> 95f5b6de5080
Removing intermediate container 577cb4556709
Step 5 : ENV DATA_DIR /data/web
 ---> Running in e472aaa991f2
 ---> d50decbaf7bc
Removing intermediate container e472aaa991f2
Step 6 : ENV LOG_DIR /data/log/nginx
 ---> Running in 5ccf9955685d
 ---> 00f1deefcc1b
Removing intermediate container 5ccf9955685d
Step 7 : RUN mkdir /data/log/nginx -p
 ---> Running in 63a61c463ad4
 ---> 467d1548998c
Removing intermediate container 63a61c463ad4
Step 8 : RUN chown nginx.nginx -R /data/log/nginx
 ---> Running in 00d559b2de7b
 ---> a550d1639d98
Removing intermediate container 00d559b2de7b
Step 9 : ADD web /data/web
 ---> e75181ff11e0
Removing intermediate container b4c03ff5df61
Step 10 : ADD nginx.conf /etc/nginx/nginx.conf
 ---> 00dcb2ec895f
Removing intermediate container 4a75f67c4f1c
Step 11 : ADD default.conf /etc/nginx/conf.d/default.conf
 ---> 9b2668d59294
Removing intermediate container d57c3e297464
Step 12 : EXPOSE 80
 ---> Running in 076209139acf
 ---> fa3247254200
Removing intermediate container 076209139acf
Step 13 : ENTRYPOINT nginx -g "daemon off;"
 ---> Running in e0a4845172cb
 ---> fa37625af3ac
Removing intermediate container e0a4845172cb
Successfully built fa37625af3ac
```

从执行过程可以看出一共十三个步骤，都完成了。

下面可以看看镜像了。
$ docker images
```
REPOSITORY                  TAG                 IMAGE ID            CREATED             SIZE
nginx_01                    latest              fa37625af3ac        38 seconds ago      182.7 MB
nginx                       latest              19146d5729dc        6 days ago          181.6 MB
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


# docker 仓库


# remark
使用dockerfile来构建自己需要的镜像，或 直接运行 run -idt 操作进入docker执行命令后 commit 操作

镜像在执行完成之后会直接关闭 所以要长期运行 要执行阻塞操作,镜像才不会关闭

使用dockerfile eg:
```
exec /usr/sbin/httpd -D FOREGROUND 
```