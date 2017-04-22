---
title: "crawler"
layout: page
date: 2017-03-15 00:00
---

# Crawler #

首先配 干货地址 

[静觅 个人博客](http://cuiqingcai.com/category/technique/python)

## 爬虫突破封禁方法

[为何大量网站不能爬取,爬虫突破封禁6种常见方法](http://mp.weixin.qq.com/s?__biz=MzIxNDE4MzA4OQ==&mid=2651024379&idx=1&sn=bd30294fb00cf22a20759503ebb0169e&scene=1&srcid=0531yCYVlBtAgDmxzZaXw87f&from=groupmessage&isappinstalled=0##)
购买 《Python网络数据采集》

- 构造合理HTTP请求头部
- 设置cookie
- 使用 Selenium 和 PhantomJS
- 正常时间访问路径
- 使用远程服务器避免IP封锁(使用TOR)

###　可能存在问题

- Javascript 调用问题
- 检查正常浏览器提交参数

### proxy ip 

1. 爬取代理网站代理ip，测试后放到自己代理ip池，供自己使用
2. 使用tor匿名方法
3. 使用ADSL拨号
4. 购买拨号VPS服务器，拨号更换IP
5. 购买手机卡上网卡设备，断开重连网络更换ip

## Selenium + PhantomJS 

### linux install 

```
pip install selenium
```

[Download Phantomjs](http://phantomjs.org/download.html)
下载对应linux 版本,解压,放到/sbin目录下,赋777权限,安装依赖库
```
yum -y install fontconfig
或
apt-get install fontconfig
```

### winodws install

[Download selenium](https://pypi.python.org/pypi/selenium)
下载 .whl 文件,安装
```
pip install selenium.whl
```

[Download Phantomjs](http://phantomjs.org/download.html)
下载对应windows版本，PhantomJS.exe到/windwos/system32 目录下

## 测试例子

```
from selenium import webdriver

driver = webdriver.PhantomJS() #webdriver.Firefox()
driver.get('http://www.waitalone.cn/')
print driver.title
driver.quit()
```


