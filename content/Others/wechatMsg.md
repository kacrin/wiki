---
title: "wxpy + send msg"
layout: page
date: 2017-08-15 00:00
---

# intro

itchat 开源的微信个人号接口  文档 [http://itchat.readthedocs.io/zh/latest/](http://itchat.readthedocs.io/zh/latest/)

itchat github [https://github.com/littlecodersh/ItChat](https://github.com/littlecodersh/ItChat)

用python玩微信 [https://github.com/youfou/wxpy](https://github.com/youfou/wxpy)

扩展

itchatmp 完备优雅的微信公众号接口，原生支持同步、协程使用 [https://github.com/littlecodersh/itchatmp](https://github.com/littlecodersh/itchatmp)

tuling123 图灵机器人 [http://www.tuling123.com](http://www.tuling123.com/)

```
#coding=utf8
import time
import json
import urllib
import urllib2
from threading import Thread, Lock
from wxpy import *
from Queue import Queue,Empty

bot = Bot(console_qr=True)
my_master = ensure_one(bot.search('查找的用户'))
tuling = Tuling(api_key='*********** api_key *********')

# 使用图灵机器人自动与所有好友聊天
#@bot.register(my_master)
@bot.register()
def reply_my_friend(msg):
    tuling.do_reply(msg)

#使用flask搭建接口返回数据
msgUrl = 'http://localhost:5000/get_wechat_msg'
msgPool = Queue()
msgState = None

def httpPost(url, data=None):
    if data:
        post_data = urllib.urlencode(data)
        request = urllib2.Request(url, post_data)
    else:
        request = urllib2.Request(url)
    request.add_header('User-Agent', 'Mozilla/5.0 (Windows NT 6.1; WOW64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/52.0.2743.116 Safari/537.36')
    request.add_header('Accept','application/json, text/javascript, */*; q=0.01')
    request.add_header('X-Requested-With','XMLHttpRequest')
    request.add_header('Content-Type','application/x-www-form-urlencoded; charset=UTF-8')

    try:
        res = urllib2.urlopen(request)
        html = res.read()
        return json.loads(html)
    except Exception,e:
        print Exception,":",e
        return False

#使用线程循环请求接口数据,收到数据压入队列中
def getWarningMsg():
    while True:
        if msgState == 'STOP':
            break
        time.sleep(3.5)
        msgArr = httpPost(msgUrl)
        #print msgArr
        if not msgArr:
            continue
        if msgArr['code'] != 0:
            continue
        if 'data' in msgArr.keys():
            for msg in msgArr['data']['msg']:
                msgPool.put(msg)

#使用线程循环推送数据
def sendWarningMsg():
    while True:
        if msgState == 'STOP':
            break
        if msgPool.qsize() > 0:
            msgData = msgPool.get()
            my_master.send(msgData)
            #print ' ===> get new message : %s ' % msgData
        time.sleep(1)

def stopGetMsg():
    global msgState
    msgState = 'STOP'

#开启进程
def startGetMsg():
    global msgState
    msgState = None
    getWarningThread = Thread(target=getWarningMsg)
    sendWarningThread = Thread(target=sendWarningMsg)
    getWarningThread.start()
    sendWarningThread.start()

startGetMsg()

embed()
```