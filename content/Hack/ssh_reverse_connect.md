---
title: "Ssh Reverse Connect"
layout: page
date: 2016-10-13 23:00
---


# ssh 突破内网反向连接 #

## 申请二级域名 ##

原3322动态域名网站
[pubyun公云](http://www.pubyun.com)

## 服务器设置 ##

服务器
```
ip: 111.112.111.112
port: 22
dns:xxx.f3322.net
```

查看路由器外网ip,绑定到 公云 申请域名,路由器设置端口映射,22端口 -> 192.168.0.100 22端口
服务器开启sshd服务,设置允许远程登录

## 内网主机设置 ##

内网主机
```
ip:192.168.0.100
port:2233
```

首先测试能否直接连接到服务器
```
$ssh root@xxx.f3322.net -p22 
```

测试成功,再测试远程端口映射
```
$ssh -NfR 1234:localhost:2233 root@xxx.f3322.net -p22
```
上一句命令,把本地2233端口绑定到服务器1234端口,使用域名会被解析成服务器外网ip
再被映射到内网

使用autossh连接,在连接各种原因断开后,自动重连 
```
$apt-get install autossh
$autossh -M 5678 -NfR 1234:localhost:2233 root@xxx.f3322.net -p 22
```
使用-f命令会在后台运行,后台运行命令后无法输入密码

使用autossh不会每次使用密码,可以使用ssh密钥登陆方式,实现自动登陆
```
$ssh-keygen
```

过程按需求输入密码,一般略过
最后在~/.ssh/下生成密钥

```
$ls ~/.ssh
id_rsa id_rsa.pub
```

## 复制密钥到服务器上 ##

```
$ cat id_rsa.pub >> ~/.ssh/authorized_keys
```

使用ssh-copy-id这个命令 过程更简单

```
$ssh-copy-id root@xxx.f3322.net
```


## 终极方案 ##

在内网机器重启时,无法启动autossh,加入daemon

以daemon方式执行，相当于root去执行autossh, ssh，这时刚才普通用户目录下的.ssh/authorized_keys文件会不起效。有两种办法解决，一种是用autossh的参数指定.ssh路径；另外一种是以普通用户身份执行daemon，下面是第二种方式。

```
/bin/su -c '/usr/bin/autossh -M 5678 -NfR 1234:localhost:2233 root@xxx.f3322.net -p22' - user1
```

autossh还有很多参数，用来设置重连间隔等等。

将上面命令放入下面各启动方式中，根据自己系统自己配置：

```
SysV：/etc/inid.d/autossh
Upstart: /etc/init/autossh.conf
systemd: /usr/lib/systemd/system/autossh.service
```


### 加入 /etc/init.d 中 ###

Debian中

在/etc/init.d/下新建autossh 文件,创建如下内容

格式按照skeleton 是一个默认模板

```
#!/bin/sh

### BEGIN INIT INFO
# Provides:          autossh
# Required-Start:    $remote_fs $syslog
# Required-Stop:     $remote_fs $syslog
# Default-Start:     2 3 4 5
# Default-Stop:      0 1 6
# Short-Description: autossh script 
# Description:       This file should be used to construct scripts to be
#                    placed in /etc/init.d.  This example start a
#                    single forking daemon capable of writing a pid
#                    file.  To get other behavoirs, implemend
#                    do_start(), do_stop() or other functions to
#                    override the defaults in /lib/init/init-d-script.
### END INIT INFO

/bin/su -c '/usr/bin/autossh -M 5678 -NfR 1234:localhost:22 root@woza.f3322.net -p22' - ninjia

```

增加执行权限

```
#chmod +x autossh
```

链接到/etc/rc2.d 

```
ln -s /etc/init.d/autossh S20autossh
```





# ssh 反向代理 + 正向代理


相信很多同学都会碰到这样一个问题。在实验室有一台机器用于日常工作，当我回家了或者回宿舍之后因为没法进入内网，所以访问不到了。如果这个时候我需要 SSH 进去做一下工作，那么怎么解决这个问题呢？本文将给出一种使用 SSH 的代理功能的解决方案。

## 机器状况
机器号       IP 	         用户名                   备注
  A        192.168.0.A 	     usr_a 	     目标服务器，在局域网中，可以访问 A
  B        B.B.B.B           usr_b 	     代理服务器，在外网中，无法访问 A
  C        -                   -         可以直接访问 B，无法直接访问 A

## C 机器使用 SSH 访问 A

在 A 机器上做到 B 机器的反向代理；在 B 机器上做正向代理本地端口转发

### 环境需求

每台机器上都需要 SSH 客户端

A、B 两台机器上需要 SSH 服务器端。通常是 openssh-server。

在 Ubuntu 上安装过程为

```
sudo apt-get install openssl-server
```

### 实施步骤

建立 A 机器到 B 机器的反向代理【A 机器上操作】

```
ssh -fCNR <port_b1>:localhost:22 usr_b@B.B.B.B
```

<port_b1> 为 B 机器上端口，用来与 A 机器上的22端口绑定。

建立 B 机器上的正向代理，用作本地转发。做这一步是因为绑定后的 端口只支持本地访问【B 机器上操作】

```
ssh -fCNL "*:<port_b2>:localhost:<port_b1>' localhost
```

<port_b2> 为本地转发端口，用以和外网通信，并将数据转发到 <port_b1>，实现可以从其他机器访问。

其中的*表示接受来自任意机器的访问。

现在在 C 机器上可以通过 B 机器 ssh 到 A 机器

```
ssh -p <portb2> usra@B.B.B.B
```

至此方案完成。

## SSH 参数解释

```
-f 后台运行
-C 允许压缩数据
-N 不执行任何命令
-R 将端口绑定到远程服务器，反向代理
-L 将端口绑定到本地客户端，正向代理
```






