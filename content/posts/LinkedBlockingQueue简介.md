---
title: LinkedBlockingQueue简介
date: 2016-08-05 10:17:46
categories: 
- Java基础
- 并发集合
tags:
- LinkedBlockingQueue
---

LinkedBlockingQueue是一个单向链表实现的阻塞队列，先进先出的顺序。支持多线程并发操作。

相比于数组实现的ArrayBlockingQueue的有界，LinkedBlockingQueue可认为是无界队列。多用于任务队列。

<!-- more -->

# 定义
LinkedBlockingQueue继承AbstractQueue，实现了BlockingQueue，Serializable接口。内部使用单向链表存储数据。

默认初始化容量是Integer最大值。

插入和取出使用不同的锁，putLock插入锁，takeLock取出锁，添加和删除数据的时候可以并行。多CPU情况下可以同一时刻既消费又生产。
# 源码分析
>jdk1.7.0_71

## put(E)方法
向队列尾部添加元素，队列已满的时候，阻塞等待。

```
public void put(E e) throws InterruptedException {
    if (e == null) throw new NullPointerException();
 
    int c = -1;
    Node<E> node = new Node(e);
    final ReentrantLock putLock = this.putLock;
    final AtomicInteger count = this.count;
    putLock.lockInterruptibly();
    try {
        
        while (count.get() == capacity) {
            notFull.await();
        }
        enqueue(node);
        c = count.getAndIncrement();
        if (c + 1 < capacity)
            notFull.signal();
    } finally {
        putLock.unlock();
    }
    if (c == 0)
        signalNotEmpty();
}
```

## offer(E)方法
向队列尾部添加元素，队列已满的时候，直接返回false。

```
public boolean offer(E e) {
    if (e == null) throw new NullPointerException();
    final AtomicInteger count = this.count;
    if (count.get() == capacity)
        return false;
    int c = -1;
    Node<E> node = new Node(e);
    final ReentrantLock putLock = this.putLock;
    putLock.lock();
    try {
        if (count.get() < capacity) {
            enqueue(node);
            c = count.getAndIncrement();
            if (c + 1 < capacity)
                notFull.signal();
        }
    } finally {
        putLock.unlock();
    }
    if (c == 0)
        signalNotEmpty();
    return c >= 0;
}
```

不做过多分析，发现下面参考处的文章写得不错，建议看下。
# 参考

[http://www.jianshu.com/p/cc2281b1a6bc](http://www.jianshu.com/p/cc2281b1a6bc)