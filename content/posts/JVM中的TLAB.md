---
title: JVM中的TLAB
date: 2017-08-21 16:24:21
categories: 
- JVM
tags:
- JVM
---

JVM中的TLAB学习

<!--more-->

TLAB，Thread Local Allocation Buffer，是加速对象分配的一种措施。对象一般都在堆上分配，堆是公用的，如果有多个线程同时申请空间分配对象，JVM就必须要进行同步才可以进行对象分配，并发较大分配效率就会降低。JVM采用TLAB来避免多线程分配对象的冲突，让线程分配对象在线程自己私有的一块堆空间中进行。

TLAB占用的是eden区的空间，每个线程都会有一个TLAB空间，线程分配对象的时候就会优先使用自己的TLAB进行分配。TLAB空间一般不会很大，大对象无法再TLAB上分配，大对象会直接分配在堆上。

TLAB被使用之后，如果新对象分配再次使用TLAB发现空间不够用时，会有两种策略：

- 废弃当前的TLAB，新建TLAB来分配对象，这样会浪费掉之前的TLAB的空闲空间
- 将新对象直接在堆上分配，旧的TLAB保留，TLAB空闲空间可以留给后面适合的对象用。

JVM采用的是两种策略结合，设置一个阈值refill_waste，如果要分配的新对象大于TLAB剩余空间并且大于refill_waste的值，则直接在堆上分配新对象；如果要分配的对象大于TLAB剩余空间并且小于refill_waste的值，则废弃当前TLAB，新建TLAB来分配新对象。

默认情况下，TLAB和refill_waste会在运行时不断调整。常用参数如下；

- `-XX:+UseTLAB`启用TLAB，默认就是启用的
- `-XX:TLABRefillWasteFraction`设置允许空间浪费的的比例，默认64，就是refill_waste等于1/64的TLAB空间大小
- `-XX:-ResizeTLAB` 禁止自动调整TLAB大小
- `-XX:TLABSize` 手动指定TLAB大小



# 参考

- 《实战Java虚拟机 JVM故障诊断与性能优化》
- 《HotSpot实战》
- 《深入理解Java虚拟机：JVM高级特性与最佳实践》