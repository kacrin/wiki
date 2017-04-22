---
title: "wiki"
layout: page
date: 2017-04-20 00:00
---

## Markdown文本自动化提交自动生成静态HTML

在服务器创建仓库,建立钩子
```
# git init --bare wiki.git
# vim wiki.git/hook/post-receive
# chomd 755 wiki.git/hook/post-receive
```
在post-receive钩子中写入
```
#!/bin/bash
git --work-tree=/home/git/wiki checkout -f
cd /home/git/wiki/
simiki g
scp -p 27551 -r /home/git/wiki/output/* root@110.122.133.144:/www/wiki
```

在本地创建git仓库及simiki目录
```
# git init 
# git remote add orgin git@110.122.134.144:/git/wiki.git
# simiki init
# vim .gitignore
```
添加过滤目录
```
/output
```

生成目录直接放在同一个服务器时，直接在钩子写,/www/wiki为web root目录
```
#!/bin/bash
git --work-tree=/www/wiki checkout -f
cd /www/wiki/
simiki g
```







