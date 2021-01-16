---
title: CyclicBarrier简介
date: 2016-06-01 14:38:32
categories: 
- Java基础
- 并发包
tags:
- CyclicBarrier
---
# CyclicBarrier简介
1. CyclicBarrier和CountDownLatch不同,是当await的数量达到了设定的数量之后,才继续往下执行	
2. CyclicBarrier数的是调用了CyclicBarrier.await()进入等待的线程数,当线程数达到了CyclicBarrier初始时规定的数目时，所有进入等待状态的线程被唤醒并继续。 CyclicBarrier就象它名字的意思一样，可看成是个障碍，所有的线程必须到齐后才能一起通过这个障碍。 CyclicBarrier初始时还可带一个Runnable的参数，此Runnable任务在CyclicBarrier的数目达到后，所有其它线程被唤醒前被执行。 

<!-- more -->

# 源码分析
>jdk1.7.0_71



# 参考
[http://xijunhu.iteye.com/blog/713433](http://xijunhu.iteye.com/blog/713433)

[http://www.cnblogs.com/techyc/archive/2013/03/13/2957059.html](http://www.cnblogs.com/techyc/archive/2013/03/13/2957059.html)
