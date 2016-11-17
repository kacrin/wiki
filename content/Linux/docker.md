---
title: "docker"
layout: page
date: 2016-11-17 23:00
---

#question 

使用数据卷地址需要命名，下次如何使用相同的路径共享到docker中
使用名字启动时，使用volume再次把路径传进去？




#DaoCloud

[国内 部署 搭建 发布 docker](https://dashboard.daocloud.io)

提供 pull 加速 永久使用

#docker 安装


#docker 镜像


#docker 容器

新建并启动容器使用 docker run 

使用容器输出hello world 然后终止容器

```
# docker run ubuntu:14.04 /bin/echo 'hello world'
```

启用一个bash终端，交互运行

```
# docker run -t -i ubuntu:14.04 /bin/bash
```

使用docker start ,直接使已终止容器运行

#docker 仓库

