---
title: Java基础之CyclicBarrier简介
date: 2016-06-01 14:38:32
categories: 
- Java基础
---
CyclicBarrier是所有线程都到了同一个集合点后，再一起同时开始执行后续操作。每个线程到达了集合点后就会等待，直到所有线程都到达了集合点。

<!--more-->

CyclicBarrier没有使用AQS框架，而是自己内部实现计数，并使用ReentrantLock和Condition来实现线程的等待和唤醒。

# 实现原理

初始化CyclicBarrier的时候指定计数count的值，每个线程执行到集合点的时候都会await操作，这里先是使用的ReentrantLock加锁，在使用ReentrantLock的条件变量Condition的await进行等待，也就是进入AQS的条件变量的等待队列中，等到最后一个线程执行await的时候，CyclicBarrier就会执行Condition的signalAll操作，这一操作会把AQS中条件变量的等待队列中的线程都转移到同步队列中，等到操作完后，ReentrantLock会释放锁，这时候AQS中会从头开始唤醒同步队列中等待的后继线程，后续每个线程唤醒后都会依次唤醒后继线程。

# 源码分析

参照源码解析：[https://github.com/dachengxi/Java8u192SourceCode](https://github.com/dachengxi/Java8u192SourceCode)