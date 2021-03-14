---
title: Java基础之AQS简介
date: 2017-03-27 16:25:44
categories: 
- Java基础
---
AQS并发包中的核心，了解其他类之前，需要先弄懂AQS，在弄懂AQS前需要先了解下管程、自选锁等基础知识。

<!--more-->

# AQS原理

AQS就是管程模型的具体实现，AQS包括：

- 一个共享变量，表示状态
- 一个同步队列，等待获取锁的线程都在队列里自旋
- 两个操作：获取锁和释放锁
- 条件变量和对应的等待队列
- 条件变量的await和signal方法

具体可参考之前的一篇：Java基础之管程介绍。

# AQS的CLH队列锁
AQS的CLH队列锁对原始的CLH队列锁做了改动，增加了前驱和后继指针，增加了waitStatus，将原始结点的state变成了队列可见的state等，但是原理还是自旋锁。具体的可参考之前的一篇：Java基础之AQS设计思路介绍。

# acquire操作

```java
if (尝试获取锁不成功) {
    node = 创建新结点，并入队列
    pred = 结点的前驱结点
    while (pred 不是头结点 || 尝试获取锁失败) {
        if (前驱结点的waitStatus是SIGNAL) {
            将当前线程挂起
        } else {
            cas设置前驱结点的waitStatus为SIGNAL
        }
         head = node
     }
 }
```

# release操作

```java
if (尝试释放锁 && 头结点的waitStatus是SIGNAL) {
    cas设置头结点waitStatus不是SIGNAL
    唤醒后继结点
}
```

# 源码

参照源码解析：[https://github.com/dachengxi/Java8u192SourceCode](https://github.com/dachengxi/Java8u192SourceCode)




