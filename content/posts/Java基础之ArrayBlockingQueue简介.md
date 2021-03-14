---
title: Java基础之ArrayBlockingQueue简介
date: 2016-05-29 18:46:59
categories: 
- Java基础
---

ArrayBlockingQueue就是一个有界阻塞队列，实现原理和生产者消费者模式一样，可以使用信号量来实现，也可以使用锁加条件变量来实现。

<!--more-->

ArrayBlockingQueue是一个有界阻塞队列，使用数组存储元素。阻塞队列是典型的生产者消费者模式。

可以先回顾下使用信号量实现生产者消费者模式的场景：使用一个信号量当做互斥锁，使用另外两个信号量当做条件变量（非空条件变量和非满条件变量）。

```java
Semaphore mutex = new Semaphore(1);
Semaphore notFull = new Semaphore(n);
Semaphore notEmpty = new Semaphore(0);
```

# 生产者方法流程

```java
// 缓冲区满的时候，生产者线程需要等待缓冲区不满
notFull.p();

// 生产者不满了，可以添加元素，需要使用互斥锁保证一次只有一个线程操作缓冲区
mutex.p();

// 将元素加入缓冲区
addElementToQueue();

// 解锁
mutex.v();

// 缓冲区中添加了新元素后，通知其他等待消费的线程现在缓冲区不空了，有元素了
notEmpty.v();
```

# 消费者方法流程

```java
// 缓冲区空的时候，消费者线程需要等待缓冲区中有元素，
notEmpty.p();

// 缓冲区有元素了，可以消费一个元素，需要使用互斥锁保证一次只有一个线程操作缓冲区
mutex.p();

// 元素从队列中移除
removeElementFromQueue();

// 解锁
mutex.v();

// 移除元素后，缓冲区中有空闲位置，通知其他等待生产的线程现在缓冲区不满了，可以生产了
notFull.v();
```

ArrayBlockingQueue实现原理也是使用一个锁和两个条件变量，对缓冲区的并发操作进行控制。这里并非使用信号量进行实现，而是使用管程模式来实现，AQS就是管程模式。使用一个可重入锁以及锁的条件变量来实现。

# 源码

参照源码解析：[https://github.com/dachengxi/Java8u192SourceCode](https://github.com/dachengxi/Java8u192SourceCode)