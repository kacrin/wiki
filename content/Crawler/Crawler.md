---
title: "crawler"
layout: page
date: 2017-03-15 00:00
---

# Crawler #

首先配 干货地址 

[静觅 个人博客](http://cuiqingcai.com/category/technique/python)
[URL-Team 数据挖掘](https://www.urlteam.org/tag/%E6%95%B0%E6%8D%AE%E6%8C%96%E6%8E%98/)
[使用tor 绕过ip限制](https://www.urlteam.org/2016/12/%E5%9F%BA%E4%BA%8Etor%E5%8C%BF%E5%90%8D%E7%BD%91%E7%BB%9C%E7%9A%84%E5%A4%9Aip%E7%88%AC%E8%99%AB/)

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
下载对应linux 版本,解压,放到/usr/bin/目录下,赋777权限
需要依赖库，安装依赖库
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

### 测试例子

```
from selenium import webdriver

driver = webdriver.PhantomJS() #webdriver.Firefox()
driver.get('http://www.waitalone.cn/')
print driver.title
driver.quit()
```


## 验证码绕过

[TensorFlow识别字母扭曲干扰型验证码-开放源码与98%模型](https://www.urlteam.org/2017/03/tensorflow%E8%AF%86%E5%88%AB%E5%AD%97%E6%AF%8D%E6%89%AD%E6%9B%B2%E5%B9%B2%E6%89%B0%E5%9E%8B%E9%AA%8C%E8%AF%81%E7%A0%81-%E5%BC%80%E6%94%BE%E6%BA%90%E7%A0%81%E4%B8%8E98%E6%A8%A1%E5%9E%8B/)