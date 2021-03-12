---
title: Java基础之自旋锁介绍
date: 2017-03-24 23:20:07
categories: 
- Java基础
---
为了更好的学习Java中的AQS，熟悉下自旋锁、排号自旋锁、MCS锁、CLH锁相关的知识。

<!--more-->

# 自旋锁

自旋锁是用于多线程之间同步的一种锁，自旋锁使用的基本思路是：如果线程获取不到锁，不会将线程挂起等待，而是一直循环检测锁是否可用。等到锁可用，就会退出循环，表示当前线程获取到锁。

## 自旋锁优点

由于等待锁的线程一直在自旋，没有挂起阻塞操作，避免了进程或者线程调度的开销。

## 自旋锁的缺点

- 由于等待锁的线程一直自旋，会浪费CPU
- 自旋锁不能保证公平性，多个线程自旋等待的时候，获取锁的时候是没有次序的，非公平

## 自旋锁适用场景

由于等待锁的线程一直自旋占有CPU，因此比较适合一些等待锁的时间很短的场景。短暂的自旋就能获取到锁，相比挂起等待的进程调度开销要小的情况下收益才会比较明显。

单核单线程CPU不适合使用自旋锁，同一时间只有一个线程在运行，如果自旋锁获取时间较长，导致获取锁的线程长时间占用CPU，浪费资源。

# 排号自旋锁

排号自旋锁解决了自旋锁的公平性问题，排号自旋锁和去银行排队办理业务类似。

排号自旋锁有一个变量叫服务号ServiceNumber，表示已经获取到锁的线程，还有一个变量叫排队号Ticket。每一个获取锁的线程都会获得一个排队号，当一个线程获取到锁的时候，服务号和排队号相等，做完操作释放锁的时候，该线程会将自己的排队号加1，并赋值给服务号，其他等待锁的线程都有自己的排队号，并且他们在自旋等待，当锁被释放时，等待的某一个线程会发现新的服务号和自己的排队号相等，于是这个线程就能获取到锁了。

利用排队号的顺序，就能让锁的获取有序，保证了公平性，FIFO。

## 排号自旋锁的优点

解决了自旋锁的公平性问题

## 排号自旋锁的缺点

在多处理器上，多个进程或者线程需要读写同一个ServiceNumber服务号，处理器核数越多，同步问题越严重，会降低系统的性能，所有等待锁的线程都在同一个共享变量上自旋，会导致频繁的CPU缓存同步。

可以使用MCS锁和CLH锁来解决排号自旋锁的问题。

# MCS锁

MCS锁是一个基于单向链表的自旋锁，保证公平性，性能高。

在MCS锁中等待锁的线程只需要在本地变量上自旋，不需要所有线程共享同一个变量，并且每个结点的直接前驱来通知结点自旋结束。由于自旋是在本地变量，不是共享变量，相比排号自旋锁，解决了共享变量导致的CPU缓存同步问题。

MCS是一个不可重入的独占锁。在NUMA系统架构中性能较好。

# CLH锁

CLH锁是一个比MCS锁更轻量的锁，基于单向链表（隐式）的自旋锁，保证公平性，性能高。

CLH锁中等待锁的线程只需要在前驱结点的本地变量进行自旋，而MCS需要在线程自己的节结点上自旋。

CLH是一个不可重入的独占锁。在SMP系统架构中性能较好

# 参考

- [https://zh.wikipedia.org/wiki/%E8%87%AA%E6%97%8B%E9%94%81](https://zh.wikipedia.org/wiki/%E8%87%AA%E6%97%8B%E9%94%81)
- [https://zh.wikipedia.org/wiki/%E6%8E%92%E5%8F%B7%E8%87%AA%E6%97%8B%E9%94%81](https://zh.wikipedia.org/wiki/%E6%8E%92%E5%8F%B7%E8%87%AA%E6%97%8B%E9%94%81)
- [https://coderbee.net/index.php/concurrent/20131115/577](https://coderbee.net/index.php/concurrent/20131115/577)
- [https://destiny1020.blog.csdn.net/article/details/79677891](https://destiny1020.blog.csdn.net/article/details/79677891)
- [https://destiny1020.blog.csdn.net/article/details/79783104](https://destiny1020.blog.csdn.net/article/details/79783104)
- [https://destiny1020.blog.csdn.net/article/details/79842501](https://destiny1020.blog.csdn.net/article/details/79842501)
- [https://blog.csdn.net/lengxiao1993/article/details/108227584](https://blog.csdn.net/lengxiao1993/article/details/108227584)
- [https://blog.csdn.net/lengxiao1993/article/details/108448199](https://blog.csdn.net/lengxiao1993/article/details/108448199)
- [https://blog.csdn.net/lengxiao1993/article/details/108449111](https://blog.csdn.net/lengxiao1993/article/details/108449111)
- [https://blog.csdn.net/lengxiao1993/article/details/108449850](https://blog.csdn.net/lengxiao1993/article/details/108449850)