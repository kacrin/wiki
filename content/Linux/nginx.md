---
title: "Centos Sever"
layout: page
date: 2017-02-09 23:00
---

# nginx

## CentOS 6下安装nginx

### centos下安装nginx

在nginx官方网站下载一个rpm包，下载地址是：[http://nginx.org/en/download.html](http://nginx.org/en/download.html)


```
wget http://nginx.org/packages/centos/6/noarch/RPMS/nginx-release-centos-6-0.el6.ngx.noarch.rpm
```

安装这个rpm包

```
rpm -ivh nginx-release-centos-6-0.el6.ngx.noarch.rpm
```

### nginx几个默认目录

```
whereis nginx
nginx: /usr/sbin/nginx /etc/nginx /usr/share/nginx
```

1 配置所在目录：/etc/nginx/
2 PID目录：/var/run/nginx.pid
3 错误日志：/var/log/nginx/error.log
4 访问日志：/var/log/nginx/access.log
5 默认站点目录：/usr/share/nginx/html

### 常用命令

```
1 启动nginx：nginx
2 重启nginx：killall -HUP nginx
2 重启nginx：nginx -s reload
3 测试nginx配置：nginx -t
```


