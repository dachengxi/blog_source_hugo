---
title: Java基础之管程介绍
date: 2017-03-24 20:40:03
categories: 
- Java基础
---
为了更好的学习Java中的AQS，先熟悉下管程以及条件变量相关的知识。

<!--more-->

信号量在使用的时候比较繁琐，并且极容易出错。如果想要实现线程间的同步，至少需要三个信号量实例，一个作为锁，两外两个充当条件变量的角色。使用复杂并且易出错，而管程解决了这些问题。

# 管程的组成

管程在信号量的基础上封装了条件变量和等待队列，还封装了同步操作。管程的组成如下：

- 一个变量，和信号量一样表示状态
- 条件变量和对应的等待队列
- 同步队列
- 条件变量可执行的await方法和signal/signalAll方法

# 管程模型

管程有三种模型：

- Hasen模型
- Hoare模型
- MESA模型

假设有两个线程t1和t2，t1等待t2的某些操作，使得t1等待的条件成立。

Hasen模型：要求将notify方法放到代码最后，也就是t2执行完后，才通知t1执行，这样就能保证同一时刻只有一个线程执行。

Hoare模型：t2执行，通知t1后，t2马上阻塞，t1执行，等t1执行完后，唤醒t2继续执行。这种模型下t2多了一次阻塞和唤醒操作。

MESA模型：t2执行，通知t1后，t2继续执行，t1不立刻执行，而是从条件变量的等待队列进入到入同步队列。t1需要使用自旋来检测条件变量，看是否满足条件。

# 管程的例子

Java中的synchronized和AQS都是基于管程模型实现的，使用的是MESA模型。

# 条件变量

条件变量是用来实现线程间的依赖或等待机制的方法，比如线程A阻塞等待某个条件才能继续执行，线程B的执行使得条件成立，就会唤醒A继续执行。

# 参考



- [https://topsli.github.io/2016/03/13/aqs.html](https://topsli.github.io/2016/03/13/aqs.html)
- [http://kexianda.info/2017/08/13/%E5%B9%B6%E5%8F%91%E7%B3%BB%E5%88%97-3-%E4%BB%8EAQS%E5%88%B0futex%E4%B9%8B%E4%B8%80-AQS%E5%92%8CLockSupport/](http://kexianda.info/2017/08/13/%E5%B9%B6%E5%8F%91%E7%B3%BB%E5%88%97-3-%E4%BB%8EAQS%E5%88%B0futex%E4%B9%8B%E4%B8%80-AQS%E5%92%8CLockSupport/)
- [http://ifeve.com/aqs/](http://ifeve.com/aqs/)
- [https://www.cnblogs.com/binarylei/p/12533857.html](https://www.cnblogs.com/binarylei/p/12533857.html)