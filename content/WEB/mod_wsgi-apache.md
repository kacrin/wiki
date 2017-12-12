---
title: "mod_wsgi(apache)"
layout: page
date: 2017-12-11 02:00
---

# mod_wsgi apahce

CentOS 7 + Apache + mod_wsgi 部署Django项目[http://www.jianshu.com/p/8a2163db7ab5](http://www.jianshu.com/p/8a2163db7ab5)

mod_wsgi(apache)

如果你正在使用 Apache 网络服务器，那么建议使用 mod_wsgi 。

务必把 app.run() 放在 if __name__ == '__main__': 内部或者放在单独 的文件中，这样可以保证它不会被调用。因为，每调用一次就会开启一个本地 WSGI 服务器。当我们使用 mod_wsgi 部署应用时，不需要使用本地服务器。
安装 mod_wsgi

可以使用包管理器或编译的方式安装 mod_wsgi 。在 UNIX 系统中如何使用源代码安装 请阅读 mod_wsgi 安装介绍 。

## install

### Ubuntu/Debian 使用下命令安装：

```
# apt-get install libapache2-mod-wsgi
```

### FreeBSD 系统中，可以通过编译 www/mod_wsgi port 或使用 pkg_add 来安装 mod_wsgi ：

```
# pkg_add -r mod_wsgi
```

如果你使用 pkgsrc ，那么可以通过编译 www/ap2-wsgi 包来安装 mod_wsgi 。

如果你遇到子进程段错误的话，不要理它，重启服务器就可以了。
创建一个 .wsgi 文件

### Centos 系统

```
# yum install -y httpd-devel

# yum install mod_wsgi

在httpd.conf文件中添加以下内容
LoadModule  wsgi_module modules/mod_wsgi.so
```

## 运行应用

### 添加wsgi文件
你需要一个 yourapplication.wsgi 文件。这个文件包含 mod_wsgi 开始时需要运行的代码，通过代码可以获得应用对象。文件中的 application 对象就 是以后要使用的应用。

对于大多数应用来说，文件包含以下内容就可以了:

```
from yourapplication import app as application
```

如果你的应用没有创建函数，只是一个独立的实例，那么可以直接把实例导入为 application 。

把文件放在一个以后可以找得到的地方（例如 /var/www/yourapplication ），并确保 yourapplication 和所有需要使用的库都位于 pythonpath 中。如果你不想在整个系统 中安装，建议使用 virtual python 实例。请记住，最好把应用安装到虚拟环境中。 有一个可选项是在 .wsgi 文件中，在导入前加入路径:

```
import sys
sys.path.insert(0, '/path/to/the/application')
```

最终
```
import sys
sys.path.insert(0, '/var/www/html/flaskr')

from app import app as application
```

配置 Apache

最后一件事是为你的应用创建一个 Apache 配置文件。基于安全原因，在下例中我们告诉 mod_wsgi 使用另外一个用户运行应用：

```
<VirtualHost *>
    ServerName example.com

    WSGIDaemonProcess yourapplication user=user1 group=group1 threads=5
    WSGIScriptAlias / /var/www/yourapplication/yourapplication.wsgi

    <Directory /var/www/yourapplication>
        WSGIProcessGroup yourapplication
        WSGIApplicationGroup %{GLOBAL}
        Order deny,allow
        Allow from all
    </Directory>
</VirtualHost>
```

注意： WSGIDaemonProcess 在 Windows 中不会被执行， 使用上面的配置 Apache 会拒绝 运行。在 Windows 系统下，请使用下面内容：

```
<VirtualHost *>
        ServerName example.com
        WSGIScriptAlias / C:\yourdir\yourapp.wsgi
        <Directory C:\yourdir>
                Order deny,allow
                Allow from all
        </Directory>
</VirtualHost>
```

