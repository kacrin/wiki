---
title: "Delay to Perform a Task"
layout: page
date: 2017-04-05 02:00
---

# 1分钟实现“延迟消息”功能

[架构师之路 - 1分钟实现“延迟消息”功能](http://mp.weixin.qq.com/s?__biz=MjM5ODYxMDA5OQ==&mid=2651959961&idx=1&sn=afec02c8dc6db9445ce40821b5336736&chksm=bd2d07458a5a8e5314560620c240b1c4cf3bbf801fc0ab524bd5e8aa8b8ef036cf755d7eb0f6&mpshare=1&scene=23&srcid=0317pp8XWI0iglZhO4SrF4Ia#rd)

## 缘起
很多时候，业务有“在一段时间之后，完成一个工作任务”的需求。
 
例如：滴滴打车订单完成后，如果用户一直不评价，48小时后会将自动评价为5星。

一般来说怎么实现这类“48小时后自动评价为5星”需求呢？
 
### 常见方案

启动一个cron定时任务，每小时跑一次，将完成时间超过48小时的订单取出，置为5星，并把评价状态置为已评价。

假设订单表的结构为：t_order(oid, finish_time, stars, status, …)，更具体的，定时任务每隔一个小时会这么做一次：
```
select oid from t_order where finish_time > 48hours and status=0;
update t_order set stars=5 and status=1 where oid in[…];
```

如果数据量很大，需要分页查询，分页update，这将会是一个for循环。
 
### 方案的不足

（1）轮询效率比较低
（2）每次扫库，已经被执行过记录，仍然会被扫描（只是不会出现在结果集中），有重复计算的嫌疑
（3）时效性不够好，如果每小时轮询一次，最差的情况下，时间误差会达到1小时
（4）如果通过增加cron轮询频率来减少（3）中的时间误差，（1）中轮询低效和（2）中重复计算的问题会进一步凸显
 
如何利用“延时消息”，对于每个任务只触发一次，保证效率的同时保证实时性，是今天要讨论的问题。
 
## 高效延时消息设计与实现

高效延时消息，包含两个重要的数据结构：
（1）环形队列，例如可以创建一个包含3600个slot的环形队列（本质是个数组）
（2）任务集合，环上每一个slot是一个Set<Task>
 
同时，启动一个timer，这个timer每隔1s，在上述环形队列中移动一格，有一个Current Index指针来标识正在检测的slot。
 
Task结构中有两个很重要的属性：
（1）Cycle-Num：当Current Index第几圈扫描到这个Slot时，执行任务
（2）Task-Function：需要执行的任务指针
 
假设当前Current Index指向第一格，当有延时消息到达之后，例如希望3610秒之后，触发一个延时消息任务，只需：
（1）计算这个Task应该放在哪一个slot，现在指向1，3610秒之后，应该是第11格，所以这个Task应该放在第11个slot的Set<Task>中
（2）计算这个Task的Cycle-Num，由于环形队列是3600格（每秒移动一格，正好1小时），这个任务是3610秒后执行，所以应该绕3610/3600=1圈之后再执行，于是Cycle-Num=1
 
Current Index不停的移动，每秒移动到一个新slot，这个slot中对应的Set<Task>，每个Task看Cycle-Num是不是0：
（1）如果不是0，说明还需要多移动几圈，将Cycle-Num减1
（2）如果是0，说明马上要执行这个Task了，取出Task-Funciton执行（可以用单独的线程来执行Task），并把这个Task从Set<Task>中删除
 
使用了“延时消息”方案之后，“订单48小时后关闭评价”的需求，只需将在订单关闭时，触发一个48小时之后的延时消息即可：
（1）无需再轮询全部订单，效率高
（2）一个订单，任务只执行一次
（3）时效性好，精确到秒（控制timer移动频率可以控制精度）
 
## 总结

环形队列是一个实现“延时消息”的好方法，开源的MQ好像都不支持延迟消息，不妨自己实现一个简易的“延时消息队列”，能解决很多业务问题，并减少很多低效扫库的cron任务。

另外，关于MQ的可达性、幂等性未来撰文另述。


## php + mysql 实现

表结构 id:自增主键 cycle_num:离近次数,为0时拿出执行 execution_time:执行时间 create_time:创建时间 task:任务方法php执行，使用curl或者cli方法执行程序
放入redis中1440,

使用php time 函数,thinkphp 框架
```
while true:
    $sql = 'select * from timing where '.time().' > execution_time';
    $arr_task = M('timing')->where(['execution_time'=>['lt',time()]])->select();
    foreach($arr_task as $key){
        if( my_curl($key['task']) ){
            M('timing')->where(['id'=>$key['id']])->delete();
        }
    }
    sleep(60)
```
my_curl为封装执行程序方法

使用 守护进程 php cli.php/Home/Index/test,执行上函数,sleep时间，根据时间需要来执行

## php + redis 实现

看业务需求，具体实现24小时后，还是多少分钟后

redis 如果是几分钟后，存入队列 3600，如果是几小时后存入队列 1440

redis存入队列，1440或3600,数组存入，结构：
```
[
    {
        'id':1,
        'list':[
            {
                'cycle_num':1,
                'task_function':'执行任务指针',
            }
        ]
    },
    {
        'id':2,
        'list':[
            {
                'cycle_num':1,
                'task_function':'执行任务指针',
            }
        ]
    },
    ...
    {
        'id':1440,
        'list':[
            {
                'cycle_num':1,
                'task_function':'执行任务指针',
            }
        ]
    },
]
```

一个变量内保存当前执行到队列id

当需要有任务延时执行时，查到到当前执行到到id，使延如果延时24小时，使用1440队列，在当前id前一id，list数组中push延时任务，cycle_num为0

执行到某个id时，判断list下各个cycle_num是不是为0，为0拿到task_function执行任务，否则使list下的cycle_num减1

