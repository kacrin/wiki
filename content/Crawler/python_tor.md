---
title: "python tor"
layout: page
date: 2017-04-25 00:00
---

# python中使用tor代理

## 环境

linux
python2.7

python 库
```
sudo pip install requests
sudo pip install requesocks
```


Tor 2.1.3 [centos install tor](http://blog.topspeedsnail.com/archives/7342)

启动tor,监听本地9050端口
```
sudo service tor start

sudo /etc/init.d/tor restart
```

生成密码，之后编辑/etc/tor/torrc加上
```
tor –hash-password mypassword
```

编辑/etc/tor/torrc 添加下面内容
```
ControlPort 9051

HashedControlPassword
16:872860B76453A77D60CA2BB8C1A7042072093276A3D701AD684053EC4C
```
让ControlPort监听9051端口，后边那个16:开头的hash就是上一步得到的。controllerPort 端口是用来控制tor

重启下tor。
```
/etc/init.d/tor restart
```

## python 示例代码

### requesocks

```
import os
import requests
import requesocks
import time

url = 'http://api.ipify.org?format=json'


def getip_requests(url):
    print "(+) Sending request with plain requests..."
    r = requests.get(url)
    print "(+) IP is: " + r.text.replace("\n", "")

def getip_requesocks(url):
    print "(+) Sending request with requesocks..."
    session = requesocks.session()
    session.proxies = {'http': 'socks5://127.0.0.1:9050','https': 'socks5://127.0.0.1:9050'}
    r = session.get(url)
    print "(+) IP is: " + r.text.replace("\n", "")

def main():
    print "Running tests..."
    getip_requests(url)
    getip_requesocks(url)
    os.system("""(echo authenticate '"sudo_pass"'; echo signal newnym; echo \quit) | nc localhost 9051""")
    time.sleep(2)
    getip_requesocks(url)

if __name__ == "__main__":
    main()
```

### PySocks

```
pip install PySocks
```

```
import socket
import socks
import requests

socks.set_default_proxy(socks.SOCKS5, "127.0.0.1", 9050)
socket.socket = socks.socksocket
print(requests.get('http://api.ipify.org?format=json').text)
```

### 跟换tor出口ip

调用下面命令即可
```
os.system("""(echo authenticate '"mypassword"'; echo signal newnym; echo \quit) | nc localhost 9051""")
```