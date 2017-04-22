---
title: "nginx"
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

### nginx 配置多域名 二级域名

参考地址：[http://www.cnblogs.com/buffer/archive/2011/08/17/2143514.html](http://www.cnblogs.com/buffer/archive/2011/08/17/2143514.html)
配置多个域名，可直接增加多个server{}

简单配置信息
```
server {
    listen       80;
    server_name  *.kacrin.site;

    location / {
        root   /www;
        index  index.html index.htm;
    }

    error_page   500 502 503 504  /50x.html;
    location = /50x.html {
        root   /usr/share/nginx/html;
    }
}

server {
    listen       80;
    server_name  wiki.kacrin.site;

    #charset koi8-r;
    #access_log  /var/log/nginx/log/host.access.log  main;

    root   /www/wiki;
    index  index.html index.htm;
}

```

