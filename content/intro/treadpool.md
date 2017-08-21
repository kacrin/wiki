---
title: "treadpool"
layout: page
date: 2017-08-14 00:00
---

# intro

python线程原理及实现 [http://www.cnblogs.com/goodhacker/p/3359985.html](http://www.cnblogs.com/goodhacker/p/3359985.html)

python线程原理及实现 [http://www.jianshu.com/p/afd9b3deb027](http://www.jianshu.com/p/afd9b3deb027)

为什么需要线程池
　　目前的大多数网络服务器，包括Web服务器、Email服务器以及数据库服务器等都具有一个共同点，就是单位时间内必须处理数目巨大的连接请求，但处理时间却相对较短。
　　传统多线程方案中我们采用的服务器模型则是一旦接受到请求之后，即创建一个新的线程，由该线程执行任务。任务执行完毕后，线程退出，这就是是“即时创建， 即时销毁”的策略。尽管与创建进程相比，创建线程的时间已经大大的缩短，但是如果提交给线程的任务是执行时间较短，而且执行次数极其频繁，那么服务器将处于不停的创建线程，销毁线程的状态。
　　我们将传统方案中的线程执行过程分为三个过程：T1、T2、T3：
　　T1：线程创建时间
　　T2：线程执行时间，包括线程的同步等时间
　　T3：线程销毁时间

　　那么我们可以看出，线程本身的开销所占的比例为(T1+T3) / (T1+T2+T3)。如果线程执行的时间很短的话，这比开销可能占到20%-50%左右。如果任务执行时间很频繁的话，这笔开销将是不可忽略的。
　　除此之外，线程池能够减少创建的线程个数。通常线程池所允许的并发线程是有上界的，如果同时需要并发的线程数超过上界，那么一部分线程将会等待。而传统方案中，如果同时请求数目为2000，那么最坏情况下，系统可能需要产生2000个线程。尽管这不是一个很大的数目，但是也有部分机器可能达不到这种要求。
　　因此线程池的出现正是着眼于减少线程池本身带来的开销。线程池采用预创建的技术，在应用程序启动之后，将立即创建一定数量的线程(N1)，放入空闲队列 中。这些线程都是处于阻塞（Suspended）状态，不消耗CPU，但占用较小的内存空间。当任务到来后，缓冲池选择一个空闲线程，把任务传入此线程中运行。当N1个线程都在处理任务后，缓冲池自动创建一定数量的新线程，用于处理更多的任务。在任务执行完毕后线程也不退出，而是继续保持在池中等待下一次的任务。当系统比较空闲时，大部分线程都一直处于暂停状态，线程池自动销毁一部分线程，回收系统资源。
　　基于这种预创建技术，线程池将线程创建和销毁本身所带来的开销分摊到了各个具体的任务上，执行次数越多，每个任务所分担到的线程本身开销则越小，不过我们另外可能需要考虑进去线程之间同步所带来的开销。

构建线程池框架

　　一般线程池都必须具备下面几个组成部分：
　　线程池管理器:用于创建并管理线程池
　　工作线程: 线程池中实际执行的线程
　　任务接口: 尽管线程池大多数情况下是用来支持网络服务器，但是我们将线程执行的任务抽象出来，形成任务接口，从而是的线程池与具体的任务无关。
　　任务队列:线程池的概念具体到实现则可能是队列，链表之类的数据结构，其中保存执行线程。

　　我们把任务放进队列中去，然后开N个线程，每个线程都去队列中取一个任务，执行完了之后告诉系统说我执行完了，然后接着去队列中取下一个任务，直至队列中所有任务取空，退出线程。

　　这就是一般的线程池实现的原理

# python realize

threadPool.py
该模块包含工作线程与线程池的实现。

```
import traceback
from threading import Thread, Lock
from Queue import Queue,Empty
import logging

log = logging.getLogger('Main.threadPool')


class Worker(Thread):

    def __init__(self, threadPool):
        Thread.__init__(self)
        self.threadPool = threadPool
        self.daemon = True
        self.state = None
        self.start()

    def stop(self):
        self.state = 'STOP'

    def run(self):
        while 1:
            if self.state == 'STOP':
                break
            try:
                func, args, kargs = self.threadPool.getTask(timeout=1)
            except Empty:
                continue
            try:
                self.threadPool.increaseRunsNum() 
                result = func(*args, **kargs) 
                self.threadPool.decreaseRunsNum()
                if result:
                    self.threadPool.putTaskResult(*result)
                self.threadPool.taskDone()
            except Exception, e:
                log.critical(traceback.format_exc())


class ThreadPool(object):

    def __init__(self, threadNum):
        self.pool = [] #线程池
        self.threadNum = threadNum  #线程数
        self.lock = Lock() #线程锁
        self.running = 0    #正在run的线程数
        self.taskQueue = Queue() #任务队列
        self.resultQueue = Queue() #结果队列
    
    def startThreads(self):
        for i in range(self.threadNum): 
            self.pool.append(Worker(self))
    
    def stopThreads(self):
        for thread in self.pool:
            thread.stop()
            thread.join()
        del self.pool[:]
    
    def putTask(self, func, *args, **kargs):
        self.taskQueue.put((func, args, kargs))

    def getTask(self, *args, **kargs):
        task = self.taskQueue.get(*args, **kargs)
        return task

    def taskJoin(self, *args, **kargs):
        self.taskQueue.join()

    def taskDone(self, *args, **kargs):
        self.taskQueue.task_done()

    def putTaskResult(self, *args):
        self.resultQueue.put(args)

    def getTaskResult(self, *args, **kargs):
        return self.resultQueue.get(*args, **kargs)

    def increaseRunsNum(self):
        self.lock.acquire() #锁住该变量,保证操作的原子性
        self.running += 1 #正在运行的线程数加1
        self.lock.release()

    def decreaseRunsNum(self):
        self.lock.acquire() 
        self.running -= 1 
        self.lock.release()

    def getTaskLeft(self):
        #线程池的所有任务包括：
        #taskQueue中未被下载的任务, resultQueue中完成了但是还没被取出的任务, 正在运行的任务
        #因此任务总数为三者之和
        return self.taskQueue.qsize()+self.resultQueue.qsize()+self.running
```