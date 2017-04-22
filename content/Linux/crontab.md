---
title: "crontab"
layout: page
date: 2017-04-21 00:00
---

# crontab

## example

```
30 21 * * * /usr/local/etc/rc.d/lighttpd restart
上面的例子表示每晚的21:30重启apache。
45 4 1,10,22 * * /usr/local/etc/rc.d/lighttpd restart
上面的例子表示每月1、10、22日的4 : 45重启apache。
10 1 * * 6,0 /usr/local/etc/rc.d/lighttpd restart
上面的例子表示每周六、周日的1 : 10重启apache。
0,30 18-23 * * * /usr/local/etc/rc.d/lighttpd restart
上面的例子表示在每天18 : 00至23 : 00之间每隔30分钟重启apache。
0 23 * * 6 /usr/local/etc/rc.d/lighttpd restart
上面的例子表示每星期六的11 : 00 pm重启apache。
0 */1 * * * /usr/local/etc/rc.d/lighttpd restart
每一小时重启apache
0 23-7/1 * * * /usr/local/etc/rc.d/lighttpd restart
晚上11点到早上7点之间，每隔一小时重启apache
0 11 4 * mon-wed /usr/local/etc/rc.d/lighttpd restart
每月的4号与每周一到周三的11点重启apache
0 4 1 jan * /usr/local/etc/rc.d/lighttpd restart
一月一号的4点重启apache
```

## use

```
crontab file [-u user]-用指定的文件替代目前的crontab。
crontab -[-u user]-用标准输入替代目前的crontab.
crontab -1 [user]-列出用户目前的crontab.
crontab -e [user]-编辑用户目前的crontab.
crontab -d [user]-删除用户目前的crontab.
crontab -c dir- 指定crontab的目录。 
```

## format

crontab文件的格式：M H D m d cmd.
M: 分钟（0-59）。
H：小时（0-23）。
D：天（1-31）。
m: 月（1-12）。
d: 一星期内的天（0~6，0为星期天）。
cmd要运行的程序，程序被送入sh执行，这个shell只有USER,HOME,SHELL这三个环境变量 

## start


Debian 使用
```
systemctl start cron
```

Centos 使用
```
service crond start
```