---
title: CountDownLatch简介
date: 2016-06-01 14:26:33
categories: 
- Java基础
- 并发包
tags:
- CountDownLatch
---

1. CountDownLatch是并发包中提供的一个可用于控制多个线程同时开始某动作的类，可以看做是一个计数器，计数器操作是院子操作，同时只能有一个线程去操作这个计数器。可以向CountDownLatch对象设置一个初始的数字作为计数值，任何调用这个对象上的await()方法都会阻塞，直到这个计数器的计数值被其他的线程减为0为止。
2. CountDownLatch的一个非常典型的应用场景是：有一个任务想要往下执行，但必须要等到其他的任务执行完毕后才可以继续往下执行。假如我们这个想要继续往下执行的任务调用一个CountDownLatch对象的await()方法，其他的任务执行完自己的任务后调用同一个CountDownLatch对象上的countDown()方法，这个调用await()方法的任务将一直阻塞等待，直到这个CountDownLatch对象的计数值减到0为止。

<!-- more -->

# 源码分析
>jdk1.7.0_71

## await()

## countDown()

# 参考
[http://zapldy.iteye.com/blog/746458](http://zapldy.iteye.com/blog/746458)

[http://www.cnblogs.com/skywang12345/p/3533887.html](http://www.cnblogs.com/skywang12345/p/3533887.html)