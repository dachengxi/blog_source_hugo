---
title: JVM中的垃圾收集器
date: 2017-09-17 19:03:34
categories: 
- JVM
tags:
- JVM
---

JVM中的垃圾收集器学习。

<!--more-->

# 串行收集器

- Serial收集器
- Serial Old收集器

## Serial收集器

Serial收集器，用于新生代，单线程进行垃圾收集，垃圾收集的时候需要暂停应用，使用复制算法。实现简单、处理高效、没有线程切换开销。

JVM在client模式下运行时，默认使用Serial收集器。可以使用`-XX:+UserSerialGC`选项来指定新生代使用Serial收集器，老年代使用Serial Old收集器

## Serial Old收集器

Serial Old收集器，用于老年代，单行线程进行垃圾收集，垃圾收集的时候需要暂停应用，使用标记整理算法。

Serial Old收集器可以和多种新生代收集器配合使用，也是CMS收集器的备用收集器。要启用老年代收集器，可以使用以下参数：

- `-XX:+UseSerialGC`新生代使用Serial收集器，老年代使用Serial Old收集器
- `-XX:+UseParNewGC`新生代使用ParNew收集器，老年代使用Serial Old收集器
- `-XX:+UseParallelGC`新生代使用Parallel Scavenge收集器，老年代使用Serial Old收集器

# 并行收集器

- ParNew收集器
- Parallel Scavenge收集器
- Parallel Old收集器

## ParNew收集器

ParNew收集器，用于新生代，多线程进行垃圾收集，垃圾收集的时候需要暂停应用，使用复制算法。

可以使用以下参数开启ParNew收集器：

- `-XX:+UseParNewGC`新生代使用ParNew收集器，老年代使用Serial Old收集器
- `-XX:+UseConcMarkSweepGC`新生代使用ParNew收集器，老年代使用CMS收集器

## Parallel Scavenge收集器

Parallel Scavenge收集器，用于新生代，多线程进行垃圾收集，垃圾收集的时候需要暂停应用，使用复制算法。

和ParNew的区别是Parallel Scavenge收集器更关注系统的吞吐量。

可以使用以下参数启用Parallel Scavenge收集器：

- `-XX:+UseParallelGC`新生代使用Parallel Scavenge收集器 ，老年代使用Serial Old收集器
- `-XX:UseParallelOldGC`新生代使用Parall Scavenge收集器，老年代使用Parallel Old收集器

控制吞吐量参数：

- `-XX:+MaxGCPauseMills`设置最大垃圾收集停顿时间
- `-XX:+GCTimeRatio`设置吞吐量大小

Parallel Scavenge收集器还支持自适应的GC调节策略。

## Parallel Old收集器

Parallel Old收集器，用于老年代，多线程进行垃圾收集，垃圾收集的时候需要暂停应用，使用标记压缩算法。

使用`-XX:+UseParallelOldGC`开启，新生代使用Parallel Scavenge收集器，老年代使用Parallel Old收集器。

Parallel Old收集器关注系统吞吐量。

# CMS收集器

CMS收集器，用于老年代，使用标记清除算法。主要关注系统停顿时间。

CMS工作主要步骤：

- 初始标记，需要暂停应用，标记根对象
- 并发标记，和用户线程并发执行，标记所有对象
- 预清理，和用户线程并发执行，清理前准备以及控制停顿时间
- 重新标记，需要暂停应用，修正并发标记数据
- 并发清理，和用户线程并发执行，清理垃圾
- 并发重置

使用`-XX:+UseConcMarkSweepGC`开启CMS，新生代使用ParNew收集器，老年代使用CMS收集器，使用Serial Old收集器作为备用收集器。

CMS收集器不会等到堆内存满的时候才进行垃圾收集，而是当堆内存到达一定阈值的时候就开始垃圾收集，确保在CMS工作过程中有足够的空间支持应用运行。`-XX:CMSInitiatingOccupancyFaction`可以指定阈值，默认68，当老年代空间使用率到达68%的时候，就会执行CMS垃圾回收。如果程序内存使用增长很快，在CMS执行过程中出现了内存不足，CMS收集就会失败，JVM将启用备用的老年代收集器Serial Old收集器进行垃圾回收，这样会导致应用程序暂停。

CMS使用标记清除算法，会产生内存碎片，可以使用`-XX:+UseCMSCompactAtFullCollection`选项，在CMS垃圾收集完成后，进行一次内存碎片整理。`-XX:CMSFullGCsBeforeCompaction`可以用于设置进行多少次CMS回收后进行一次内存压缩。

# G1收集器

G1，Garbage-First收集器，是JDK1.7中新的垃圾收集器。关注最小时延的垃圾收集器

# 参考

- 《深入Java虚拟机》
- 《实战Java虚拟机 JVM故障诊断与性能优化》
- 《HotSpot实战》
- 《深入理解Java虚拟机：JVM高级特性与最佳实践》
- 《Java虚拟机规范》
- [https://cloud.tencent.com/developer/article/1459638](https://cloud.tencent.com/developer/article/1459638)
- [https://ericfu.me/g1-garbage-collector/](https://ericfu.me/g1-garbage-collector/)