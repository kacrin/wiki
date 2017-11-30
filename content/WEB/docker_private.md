---
title: "docker private"
layout: page
date: 2017-11-29 00:00
---

# Docker 私有化 搭建

> * Docker 安装
> * 导入镜像 
> * 启动容器
> * 进入 Docker 配置

---
## Docker 安装

安装 docker
```
# yum -y install docker
```
启动服务
```
# systemctl start docker
```
## 上传导入镜像

上传镜像  本地使用 scp 上传
```
# scp imagename.tar root@122.22.22.22:/root/imagename.tar
```
导入镜像
```
# docker load imagename.tar
```
查看镜像
```
# docker images
```
代码 映射，文件夹赋予权限
```
# scp name_code.tar root@122.22.22.22:/var/www/html/name_code.tar
# tar -xvf name_code.tar
# rm -rf /var/www/html/src/runtime/*
# chmod 777 /var/www/html/src/runtime
# chomd 777 /var/www/html/builds
# chomd 777 /var/www/html/uploadfiles
```
配置config（路径是否正确，是否为相对路径）
```
# vim /var/www/html/src/config/*
```
跑批shell脚本放到
```
# /var/www/html/shell 路径下
```

启动容器 
```
# docker run --name mongo_server -v /var/mongo:/data/db -d mongo:laster
# docker run -idt -p 80:80 -p 127.0.0.1:1022:22 -v /var/www/html:/var/www/html --link mongo_server:mongo  dockername/private:v2
```
> -p : 映射本地到容器端口
> -v : 映射文件/夹
> --link : 链接容器

映射 本地 0.0.0.0:80 端口到容器 80 端口，映射本地 127.0.0.1:1022 到容器 22 端口

## 进入容器

```
# ssh root@localhost -p1022
# passwd : password
```
容器密码为 password

## mysql 设置远程访问

授权用户,你想root使用密码从任何主机连接到mysql服务器
```
GRANT ALL PRIVILEGES ON *.* TO 'root'@'%'  IDENTIFIED BY 'admin123'  WITH GRANT OPTION;
flush privileges;
```

如果你想允许用户root从ip为192.168.1.104的主机连接到mysql服务器
```
GRANT ALL PRIVILEGES ON *.* TO 'myuser'@'192.168.1.104'   IDENTIFIED BY 'admin123'  WITH GRANT OPTION; 
flush privileges;
```

# Docker 构建

> * 拉取 centos7 镜像
> * 编译 screw plus 扩展
> * 安装 apache , php , sshd 服务
> * Dockerfile 构造启动容器
> * Docker 容器镜像导出

## 拉取镜像

安装，启动 Docker 服务步骤省略

```
# docker pull centos
# docker pull mongo  //mongodb数据库
```

## 编译加密扩展

运行交互式容器
```
# docker run -idt centos:latest
```
>-t : 在新容器内指定一个伪终端或终端。
>-i : 允许你对容器内的标准输入 (STDIN) 进行交互。
>-d : 后台运行

查看 启动容器
```
# docker ps 
```

进入容器
```
# docker attach <CONTAINER ID>/<NAMES> 
```
>CONTAINER ID : 容器ID
>NAMES : 自动分配的容器名称

安装 php apache
```
# yum -y install httpd
# yum -y install php php-mysql php-gd libjpeg* php-ldap php-odbc php-pear php-xml php-xmlrpc php-mbstring php-bcmath php-mhash
```
安装 mcrypt加密 php-mcrypt 扩展 php-mongo 扩展
```
# yum  install epel-release  //扩展包更新包
# yum  update //更新yum源
# yum install libmcrypt libmcrypt-devel mcrypt mhash 
# yum install php-mcrypt //按照php mcrypt扩展
# yum install php-mongo
```

webservice soap扩展 ，与 webservice 接口交互，接口使用webservice
```
# yum install php-soap
```
代码使用
```
try{
    $soap = new \SoapClient('http://192.168.1.111:8070/shortmessage?wsdl');
    $postFields = [
        'sendMessage' => [
            'password' => 'passwd',
            'source' => 'source',
            'phone' => '13987654321',
            'message' => urldecode ( '发送内容' )
        ]
    ];
    $output = $soap->__soapCall("sendMessage", $postFields);
}catch (\SoapFault $e){
    $errorMsg = $e->getMessage();
}catch (Exception $e){
    $errorMsg = $e->getMessage();
}
```

安装编译环境
```
# yum -y install gcc g++ make
```

编译安装 screw-plus
查看  (  http://www.jianshu.com/p/f6425e2f8643  )

screw 放到本地 /usr/sbin/ 中（screw 可解密代码）
```
# mv screw /usr/sbin/
```

加密代码
```
# screw /src/xxx 
# screw /src/xxx -d //解密代码
```
保留不加密文件 底层， composer安装扩展， /src/config 中配置文件   

安装git
```
# yum -y install git
```

安装sshd 为 sshd 服务生成密钥
```
# yum -y install openssh-server openssh-client
# ssh-keygen -t rsa -f /etc/ssh/ssh_host_rsa_key
# ssh-keygen -t ecdsa -f /etc/ssh/ssh_host_ecdsa_key
# ssh-keygen -t ed25519 -f /etc/ssh/ssh_host_ed25519_key
```
安装 crontabs 服务，添加 crontab 脚本
```
# yum -y install crontabs
# crontab -e
0 0 * * * /var/www/html/shell/daily.sh
0 1 * * * /var/www/html/shell/big-data.sh
*/15 * * * * /var/www/html/shell/push.sh
0 0 1 * * /var/www/html/shell/month.sh
0 */1 * * * /var/www/html/shell/clock.sh
# crontab -l
```

修改时区 设置为上海时区 ( 定时任务需要 )
```
cp /usr/share/zoneinfo/Asia/Shanghai /etc/localtime
```
修改php.ini 文件最大上传大小
```
upload_max_filesize = 20M
```
修改php.ini session失效时间为8小时
```
session.gc_maxlifetime = 28800
```

关闭Apache的目录浏览 配置文件httpd.conf
```
查找 
Options Indexes FollowSymLinks
修改为 
Options -Indexes 
```
apache开启 所有路径 url 重写 ，httpd.conf
```
找到 #LoadModule rewrite_module modules/mod_rewrite.so 去掉前面的#
找到 AllowOverride None 改成 AllowOverride All , 有两个全部改掉
```

编写启 run.sh 启动脚本
```
cat >> run.sh << EOF 
#!/bin/bash 
rm -rf /run/httpd/*
exec /usr/sbin/sshd -D &
exec /usr/sbin/crond &
exec /usr/sbin/apachectl -D FOREGROUND 
EOF 
```
确保最后一条服务阻塞状态，最后一条服务前非阻塞（后台运行）

退出容器
```
# exit
```

## DockerFile 构建容器

把刚运行容器保存为镜像
```
# docker commmit -m "提交保存注释" <CONTAINER ID> [REPOSITORY[:TAG]]
```
> CONTAINER ID : 容器ID
> [REPOSITORY[:TAG]] : 保存镜像名称

dockerfile 文件内容
```
FROM dockername/close:v1   //使用镜像名称
MAINTAINER jooyum    //作者
CMD ["/run.sh"]
```
dockerfile 命令详解 （  [https://www.tuicool.com/articles/zeeyQbi](https://www.tuicool.com/articles/zeeyQbi)  ）

构建容器
```
# docker build filepath [REPOSITORY[:TAG]]
```
> filepath 存放 dockerfile 路径，路径下放入 dockerfile 文件


## 打包导出镜像

```
# docker save [REPOSITORY[:TAG]] > /root/dockername.tar
```

# REMARK

- 构建docker启动自动运行代码，必须使用dockerfile来构建
- 操作完容器时忘记commit为新镜像，再dockerfile构建时未达到预想效果
- docker 容器启动运行程序必须为阻塞，否则容器启动执行完程序后容器关闭


# Git 代码管理

## git版本控制器实现流程
> 1. 提交源码到 /git/data.git 仓库
> 2. 编写 /git/data.git 钩子，使用 git checkout 代码更新到 /git/encrytion/cache , 使用 screw 加密代码 ， git add - commit 操作提交到 /git/encrytion/data.git
> 3. 编写 /git/encrytion/data.git 钩子，curl 请求需要更新服务器的接口
> 4. 接口 使用 php exec 函数操作 git 拉下代码


## 创建仓库

创建用户
```
# useradd git
# useradd company
```
禁用shell登录, 创建的git用户不允许登录shell，编辑/etc/passwd
```
git:x:1001:1001:,,,:/home/git:/bin/bash
改为：
git:x:1001:1001:,,,:/home/git:/usr/bin/git-shell
```

创建 源码仓库
```
# git init --bare /git/data.git
# chown git:git /git/data.git -R
```
创建 加密仓库
```
# git init --bare /git/encryption/data.git
# chown company:company /git/encryption/data.git -R
```
创建 临时代码加密缓存文件夹
```
# mkdir /git/encryption/cache
```

## 编译 screw 

```
git clone https://github.com/del-xiong/screw-plus
cd screw-plus
cd tools
make
```
提示 linux /usr/bin/ld cannot find -lz
> 解决文件文章 
> https://www.liberobk.com/linux/432.html
> http://www.cnblogs.com/z-books/p/4171195.html

查找 libz.so 发现存在 libz.so.1 ，软链 libz.so 到 libz.so.1
```
find -name "libz.so*"
ls -la /lib64/libz.so.1
ln -s /lib64/libz.so.1 /lib64/libz.so

find -name 
/usr/lib
```
编译成功
```
make
mv screw /user/sbin/
```

## git钩子

首先为 git 添加可使用 sudo 命令执行 root 命令
```
# chomd 777 /etc/sudoers
# vim /etc/sudoers

## Allow root to run any commands anywhere 
root    ALL=(ALL)       ALL
git   ALL=(ALL)      NOPASSWD:ALL

# chomd 440 /etc/sudoers
```
注释掉
```
Defaults    requiretty
修改为 
#Defaults    requiretty，表示不需要控制终端。
```
必须把 /etc/sudoers 文件改回440权限，要不提示该文件为可写状态，无法执行 sudo 命令

### 源码仓库钩子
```
# mv /git/data.git/hooks/post-receive.sample /git/data.git/hooks/post-receive
```
源码仓库 hooks/post-receive 内容
```
#!/bin/sh

# 把仓库强制覆盖到 /data/git/encryption/baheal_cache 路径下
git --work-tree=/data/git/encryption/baheal_cache checkout -f 

# 把路径切换到 /data/git/encryption/baheal_cache 下
cd /data/git/encryption/baheal_cache
# 加密文件
/usr/sbin/screw src/commands
/usr/sbin/screw src/components
/usr/sbin/screw src/extend
/usr/sbin/screw src/modules
/usr/sbin/screw src/models
# 删除git工作路径
unset GIT_DIR
# 代码更新 提交到加密仓库
sudo git add *
sudo git commit -m "update 123"
sudo git push origin master

sudo chown -R baheal:baheal /data/git/encryption/baheal.git
```
> 无法切换 git 工作路径问题
> https://stackoverflow.com/questions/4043609/getting-fatal-not-a-git-repository-when-using-post-update-hook-to-execut

### 加密仓库钩子

加密仓库 hooks/post-receive 内容
```
#!/bin/bash
#
curl http://150.138.155.205:8096/src/main/index/update-code
```

## Docker 仓库中操作

两篇文章 apache 权限执行 git 命令
> http://www.cnblogs.com/hf8051/p/4511155.html
> http://www.metsky.com/archives/843.html

安装 sudo 命令，为 apache 可执行 root 命令
```
# yum -y install sudo
```
添加 apache 用户的 root 执行权限
```
# chomd 777 /etc/sudoers
# vim /etc/sudoers

## Allow root to run any commands anywhere 
root    ALL=(ALL)       ALL
apache   ALL=(ALL)      NOPASSWD:ALL

# chomd 440 /etc/sudoers
```
设置 用户目录 .ssh 文件夹（执行sudo命令实质使用/root/.ssh路径的密钥）
```
# 此步骤 略掉
# mkdir /usr/share/httpd/.ssh
# chmod 700 /usr/share/httpd/.ssh
# chown apache:apache /usr/share/httpd/.ssh -R 
```
生成ssh密钥 加入免登录
```
# scp -P 1022 ~/.ssh/id_rsa* root@localhost:/root/.ssh 
```
> apache 执行权限问题
> http://www.cnblogs.com/hf8051/p/4511155.html
> http://www.metsky.com/archives/843.html

## php 代码部分
php 代码 sudo 执行 root 命令（否则无法使用linux下git命令），执行脚本 update.sh
```
$output = exec("/bin/sudo /bin/bash /var/www/html/shell/update.sh",$a, $b);
var_dump($output);
var_dump($a);
var_dump($b);
```
update.sh 内容
```
#!/bin/bash

cd /var/www/html/
git pull origin master
```



