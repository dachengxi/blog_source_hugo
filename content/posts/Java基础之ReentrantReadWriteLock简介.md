---
title: Java基础之ReentrantReadWriteLock简介
date: 2017-04-08 20:00:51
categories: 
- Java基础
---
ReentrantReadWriteLock是可重入的读写锁，比ReentrantLock多了一把读锁，实现上基本类似，主要还是使用AQS。

<!--more-->

ReentrantReadWriteLock拥有两把锁，一把读锁、一把写锁，同一时刻允许多个线程对共享资源进行读操作，但同一时刻只允许一个线程对共享资源进行写操作，也就是：读读共享、写写互斥、读写互斥。

ReentrantReadWriteLock有两把锁，理论上是需要两个状态变量来控制，但是它依旧使用AQS的state来控制，所以把state分成了两部分：state的高16位代表读锁，低16位代表写锁状态。

读锁和写锁的加锁以及解锁操作都和ReentrantLock类似，还是AQS那一套逻辑。

ReentrantReadWriteLock适用于读多写少的场景，但需要注意在非公平模式下，如果有大量的读操作，可能会导致少量的写一直在同步队列中等待，造成写操作的饥饿现象发生。StampedLock可以解决这个问题。

# 源码

参照源码解析：[https://github.com/dachengxi/Java8u192SourceCode](https://github.com/dachengxi/Java8u192SourceCode)