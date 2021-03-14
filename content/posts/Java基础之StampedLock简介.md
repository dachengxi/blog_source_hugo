---
title: Java基础之StampedLock简介
date: 2017-04-08 20:00:51
categories: 
- Java基础
---
ReentrantReadWriteLock适合读多写少场景，但是可能会产生线程饥饿的问题，使用StampedLock可以解决ReentrantReadWriteLock的缺点。

<!--more-->

ReentrantReadWriteLock读写锁中读读共享，读写和写写互斥，如果有读锁则写锁需要等待读锁释放，在非公平模式下，如果有大量的读线程进来，可能会导致写线程一直获取不到锁，导致写线程饥饿问题。

# 顺序锁

StampedLock解决了ReentrantReadWriteLock的写线程饥饿问题，了解StampedLock之前先了解下SeqLock（顺序锁，sequence lock）

SeqLock可以保证写线程的优先，让写线程更快的获取到锁，而不是在有读线程加锁的情况下，写线程需要等待。

顺序锁的设计思想：
读线程在读取共享数据的时候不需要加锁，只有写线程在需要修改共享数据的时候加锁，读线程和写线程之间引入一个整形变量sequence，写线程在进入临界区的时候会将sequence加1，写线程在退出临界区的时候sequence还会再加1。当sequence为奇数的时候，表示有写操作正在进行，读操作需要等待，直到sequence变为偶数；当读操作进入临界区时，需要先记录下sequence的值，等退出临界区的时候，会和当前的sequence进行比较，如果不相等则表示读操作在临界区时，有写操作发生，这时候读操作需要重试重新读取。

顺序锁中读写是不互斥的，只有写写是互斥的。

顺序锁的写操作获取锁的过程：

1. 写操作获取锁，确保只有一个写线程进入临界区
2. sequence加1

释放锁的过程

1. 释放锁
2. sequence加1

读操作：

1. 获取sequence，如果是偶数则可以进入临界区；如果是奇数则等待变为偶数
2. 进入临界区
3. 获取sequence和老的sequence比较，如果相等则完成，否则重试

顺序锁适用于读多写少的场景，写操作较少但是写操作性能要求较高，也就是写操作随时可以更新。

# StampedLock

StampedLock没有使用AQS框架，但是设计思路还是和AQS类似，并且使用的队列也是CLH队列的修改版。

StampedLock中有三种方式：

- 独占写，和读写锁的写类似，写写互斥，写和悲观读互斥
- 悲观读，和读写锁的读类似，读读共享，读写互斥
- 乐观读，就是顺序锁中的读，使用sequence来判断数据是否被修改过

使用StampedLock的读操作的建议：乐观读时，如果写操作修改了共享资源，则升级乐观读为悲观读锁，这样可用避免乐观读反复的循环等待写锁的释放，造成CPU资源的浪费。

StampedLock不支持重入、不支持条件变量。

# 源码

参照源码解析：[https://github.com/dachengxi/Java8u192SourceCode](https://github.com/dachengxi/Java8u192SourceCode)

# 参考

- [https://blog.csdn.net/zhoutaopower/article/details/86611798](https://blog.csdn.net/zhoutaopower/article/details/86611798)
- [https://blog.csdn.net/longwang155069/article/details/52231519](https://blog.csdn.net/longwang155069/article/details/52231519)
- [http://www.yebangyu.org/blog/2016/06/26/sequence-lock/](http://www.yebangyu.org/blog/2016/06/26/sequence-lock/)
- [https://cloud.tencent.com/developer/article/1021135](https://cloud.tencent.com/developer/article/1021135)