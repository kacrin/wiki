---
title: "GitLab"
layout: page
date: 2017-09-17 20:00
---

# Intro

使用docker搭建gitlab服务器[https://wayearn.com/2016/11/use-docker-tobuild-gitlab-server/](https://wayearn.com/2016/11/use-docker-tobuild-gitlab-server/)

使用Docker搭建GitLab实践[http://www.open-open.com/lib/view/open1438009785316.html](http://www.open-open.com/lib/view/open1438009785316.html)

github - docker-gitlab[https://github.com/sameersbn/docker-gitlab](https://github.com/sameersbn/docker-gitlab)

使用docker搭建gitlab服务器，docker install 略过

## 环境

Centos 7
Docker 1.12.6
gitlab 8.13.6


# Install

```
docker pull sameersbn/gitlab:8.14.0
docker pull sameersbn/postgresql:9.5-3
docker pull sameersbn/redis:latest
```

启动postgresql数据库
```
docker run --name gitlab-postgresql -d \
--env 'DB_NAME=gitlabhq_production' \
--env 'DB_USER=gitlab' --env 'DB_PASS=password' \
--env 'DB_EXTENSION=pg_trgm' \
--volume /srv/docker/gitlab/postgresql:/var/lib/postgresql \
sameersbn/postgresql:9.5-3
```

启动redis数据库
```
docker run --name gitlab-redis -d \
--volume /srv/docker/gitlab/redis:/var/lib/redis \
sameersbn/redis:latest
```

启动gitlab服务
```
docker run --name gitlab -d \
--link gitlab-postgresql:postgresql --link gitlab-redis:redisio \
--publish 10022:22 --publish 10080:80 \
--env 'GITLAB_HOST=www.example.com' \
--env 'GITLAB_PORT=10080' --env 'GITLAB_SSH_PORT=10022' \
--env 'GITLAB_SECRETS_DB_KEY_BASE=long-and-random-alpha-numeric-string' \
--env 'GITLAB_SECRETS_SECRET_KEY_BASE=long-and-random-alpha-numeric-string' \
--env 'GITLAB_SECRETS_OTP_KEY_BASE=long-and-random-alpha-numeric-string' \
--volume /srv/docker/gitlab/gitlab:/home/git/data \
sameersbn/gitlab:8.14.0
```

env环境变量，volume相当于把容器内的文件挂载到宿主里面来，就像U盘插到了系统中，link连接容器，让他们通信，publish发布，做一个端口映射

GITLAB_HOST 参数，必须要填写，不填写，在repo的地址会成为localhost

# Docker-compose

快速启动容器

## Install

```
curl -L "https://github.com/docker/compose/releases/download/1.8.1/docker-compose-$(uname -s)-$(uname -m)" -o /usr/local/bin/docker-compose
```
 
权限赋予：

```
chmod +x /usr/local/bin/docker-compose
```

查看：

```
docker-compose --version
docker-compose version: 1.8.1
```
 
PS：或者使用pip安装：

```
pip install docker-compose
```
 
## 安装gitlab的docker-compose配置文件

```
wget https://raw.githubusercontent.com/sameersbn/docker-gitlab/master/docker-compose.yml
```
 
## 配置

### 配置gitlab的密钥

[https://github.com/sameersbn/docker-gitlab/blob/master/docker-compose.yml#L57-L59](https://github.com/sameersbn/docker-gitlab/blob/master/docker-compose.yml#L57-L59)

```
- GITLAB_SECRETS_DB_KEY_BASE=long-and-random-alphanumeric-string
- GITLAB_SECRETS_SECRET_KEY_BASE=long-and-random-alphanumeric-string
- GITLAB_SECRETS_OTP_KEY_BASE=long-and-random-alphanumeric-string
```

在long-and-random-alphanumeric-string配置一长串密钥。

### 配置Gitlab_host

[https://github.com/sameersbn/docker-gitlab/blob/master/docker-compose.yml#L53](https://github.com/sameersbn/docker-gitlab/blob/master/docker-compose.yml#L53)

其他的，如端口的映射、数据库密码，大家按需配置。

### 邮件配置

```
- SMTP_ENABLED=true
- SMTP_DOMAIN=email-smtp.region-1.amazonaws.com
- SMTP_HOST=email-smtp.region-1.amazonaws.com
- SMTP_PORT=587
- SMTP_USER=IAMmailerKey
- SMTP_PASS=IAMmailerSecret
- SMTP_STARTTLS=true
- SMTP_AUTHENTICATION=login
```
 

建议使用AWS，因为mailgun国内已经被墙了。而163，QQ这些公共邮箱，每天都有发件限制。https://gitlab.com/gitlab-org/omnibus-gitlab/blob/master/doc/settings/smtp.md

### 关于时区设置（选择北京时区）

```
- TZ=Asia/Beijing
- GITLAB_TIMEZONE=Beijing
```

### 关于备份

```
- GITLAB_BACKUP_SCHEDULE=daily
- GITLAB_BACKUP_TIME=02:00
- GITLAB_BACKUP_DIR=/home/git/data/backups
```
 

PS：每天2点备份，备份地址：/home/git/data/backups

## 启动

```
docker-compose up -d
```
	
# Visit

地址：http://www.example.com:10080
默认的用户名：root
默认的密码：5iveL!fe
