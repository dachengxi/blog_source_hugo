---
title: Java基础之Mutex介绍
date: 2017-03-23 16:25:44
categories: 
- Java基础
---
为了更好的学习Java中的AQS，先回顾下基础知识mutex互斥锁。

<!--more-->

# 互斥锁的定义

在多线程编程中，为了防止多个线程同时对临界区代码进行访问，操作系统引入了互斥锁。对于临界区代码的访问，同一时刻只能有一个线程进行。

# 互斥锁的实现原理

互斥锁可以理解为是一个整形变量和两个操作（加锁和解锁）构成的锁。

互斥锁只有两个状态：

- 未加锁状态
- 加锁状态

互斥锁加锁的实现逻辑：先判断锁的状态，如果是未加锁状态，则将锁改为加锁状态，并返回成功；如果是已加锁状态，则挂起等待。

互斥锁解锁的实现逻辑：直接将锁改为未加锁状态，并唤醒那些挂起等待的的线程。

# 互斥锁的底层支持

在互斥锁的加锁和解锁实现逻辑中可以看到，加锁和解锁过程中是有多个步骤的，这需要操作系统保证互斥锁操作的原子性。

CPU提供的支持有：

- 提供原子操作（Test And Set）
- 关闭中断
- 锁内存总线

操作系统利用CPU提供的支持，就可以实现互斥锁。

# 互斥锁的使用

互斥锁的加锁和解锁只能由同一个线程进行操作。

# Mutex和优先级反转问题

优先级反转是指：一个低优先级的任务持有一个共享资源，一个高优先的任务也需要持有该共享资源，但此时共享资源被低优先级的任务持有，故高优先级的任务需要阻塞等待低优先级的任务释放资源，而在这个过程中，有一个中优先级的任务（这个任务不需要持有该共享资源）到来，抢占了CPU时间，这样就导致中优先级的任务先执行了，甚至可能会导致高优先级的任务无法执行。

解决优先级反转的方案有：

- 优先级继承，当一个低优先级的任务持有资源，此时高优先级任务等待低优先级任务完成时，将低优先级任务的优先级调整为高优先级任务的优先级，等低优先级任务释放共享资源后，再回到原来的优先级。
- 设置优先级上线，给访问共享资源的任务一个高优先级。
- 禁止中断

实时操作系统上，如果需要互斥保护，进行使用mutex，而不是semaphore，因为semaphore一般没有优先级继承，会导致优先级反转。

# 参考

- [https://blog.csdn.net/Onlyonefate/article/details/52183908?utm_medium=distribute.pc_relevant.none-task-blog-baidujs_title-0&spm=1001.2101.3001.4242](https://blog.csdn.net/Onlyonefate/article/details/52183908?utm_medium=distribute.pc_relevant.none-task-blog-baidujs_title-0&spm=1001.2101.3001.4242)
- [https://zh.wikipedia.org/wiki/%E4%BA%92%E6%96%A5%E9%94%81](https://zh.wikipedia.org/wiki/%E4%BA%92%E6%96%A5%E9%94%81)
- [https://zhuanlan.zhihu.com/p/146132061](https://zhuanlan.zhihu.com/p/146132061)
- [https://developer.aliyun.com/article/449687](https://developer.aliyun.com/article/449687)
- [https://blog.csdn.net/iteye_21199/article/details/82474836](https://blog.csdn.net/iteye_21199/article/details/82474836)
- [https://www.cnblogs.com/sky-heaven/p/5016222.html](https://www.cnblogs.com/sky-heaven/p/5016222.html)




