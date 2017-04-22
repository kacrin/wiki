---
title: "Debian"
layout: page
date: 2017-04-21 00:00
---


# Debian

## update-rc.d

### 删除一个服务

如果你想手动的完全禁用Apache2服务，你需要删除其中的所有在/etc/rcX.d中的单一链路。但是如果使用update-rc.d，则非常简单： 
```
update-rc.d -f apache2 remove
```

参数-f是强制删除符号链接，即使/etc/init.d/apache2仍然存在。 Note：这个命令仅仅禁止该服务，直到该服务被升级。如果你想在服务升级后仍然保持被禁用。应该执行如下的命令：
```
update-rc.d apache2 stop 80 0 1 2 3 4 5 6 .
```

### 增加一个服务

如果你想重新添加这个服务并让它开机自动执行，你需要执行以下命令： 
```
update-rc.d apache2 defaults
```

并且可以指定该服务的启动顺序：
```
update-rc.d apache2 defaults 90
```

还可以更详细的控制start与kill顺序：
```
update-rc.d apache2 defaults 20 80
```

其中前面的20是start时的运行顺序级别，80为kill时的级别。也可以写成：
```
update-rc.d apache2 start 20 2 3 4 5 . stop 80 0 1 6 .
```
其中0～6为运行级别。 update-rc.d命令不仅适用Linux服务，编写的脚本同样可以用这个命令设为开机自动运行 