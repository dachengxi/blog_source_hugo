---
title: Semaphore简介
date: 2016-06-01 13:25:04
categories: 
- Java基础
- 并发包
tags:
- Semaphore
---
# Semaphore简介
1. Semaphore是并发包中提供的用于控制某资源同时被访问的个数
2. 操作系统的信号量是个很重要的概念，在进程控制方面都有应用。Java 并发库 的Semaphore 可以很轻松完成信号量控制，Semaphore可以控制某个资源可被同时访问的个数，通过 acquire() 获取一个许可，如果没有就等待，而 release() 释放一个许可。
3. Semaphore维护了当前访问的个数，提供同步机制，控制同时访问的个数
4. Semaphore类只是一个资源数量的抽象表示,并不负责管理资源对象本身,可能有多个线程同时获取到资源使用许可,因此需要使用同步机制避免数据竞争.

<!-- more -->

# 源码分析
>jdk1.7.0_71

## Semaphore(int permits)
```
//指定许可数初始化,非公平模式
public Semaphore(int permits) {
        sync = new NonfairSync(permits);
    }
```

## Semaphore(int permits,boolean fair)
```
//可在初始化时指定第二个参数为true,使用公平模式
public Semaphore(int permits, boolean fair) {
        sync = fair ? new FairSync(permits) : new NonfairSync(permits);
    }
```

## acquire() 阻塞,获取许可,可以被中断
```
public void acquire() throws InterruptedException {
        sync.acquireSharedInterruptibly(1);
    }
```

## acquireUninterruptibly()获取许可,不可中断
```
public void acquireUninterruptibly() {
        sync.acquireShared(1);
    }
```

## tryAcquire()非阻塞,获取许可
```
public boolean tryAcquire() {
        return sync.nonfairTryAcquireShared(1) >= 0;
    }
```

## tryAcquire(long timeout,TimeUnit unit)非阻塞,获取许可
```
public boolean tryAcquire(long timeout, TimeUnit unit)
        throws InterruptedException {
        return sync.tryAcquireSharedNanos(1, unit.toNanos(timeout));
    }
```

## release()释放许可
```
public void release() {
        sync.releaseShared(1);
    }
```

# 参考
[http://www.cnblogs.com/whgw/archive/2011/09/29/2195555.html](http://www.cnblogs.com/whgw/archive/2011/09/29/2195555.html)

