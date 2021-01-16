---
title: JVM中FullGC耗时过高
date: 2017-06-24 23:49:09
categories: 
- JVM
tags:
- JVM
---

分析一下FullGC耗时过高的原因

<!--more-->

暂时还没遇到实际例子，后面生产环境遇到了之后再来补充。

针对不同的垃圾收集器，Full GC的触发条件可能不都一样。按HotSpot VM的serial GC的实现来看，触发条件是：

- 当准备要触发一次Young GC时，如果发现统计数据说之前Young GC的平均晋升大小比目前老年代剩余的空间大，则不会触发Young GC而是转为触发Full GC（因为HotSpot VM的GC里，除了CMS的并发收集之外，其它能收集老年代的GC都会同时收集整个GC堆，包括新生代，所以不需要事先触发一次单独的Young GC）。
- 如果有永久代（方法区）的话，要在永久代分配空间但已经没有足够空间时，也要触发一次Full GC。
- System.gc()、heap dump带GC，默认也是触发Full GC。
- 老年代空间不足，新生代对象转入以及创建大对象大数组时，空间不足，会导致Full GC；或者空间足够，但是没有足够的连续空间存放对象，会导致Full GC。CMS垃圾收集器提供了一个可配置的参数`-XX:+UseCMSCompactAtFullCollection`，用于在Full GC后进行碎片整理的，内存整理的过程无法并发的，需要stop the world，JVM还提供了另外一个参数`-XX:CMSFullGCsBeforeCompaction`用于设置在执行多少次不压缩的Full GC后，跟着来一次带压缩的。
- CMS GC出现promotion failed和concurrent mode failure会导致Full GC，promotion failed是在进行YGC时，survivor区放不下、对象只能放入老年代，而此时老年代也放不下造成的；concurrent mode failure是在执行CMS GC的过程中同时有对象要放入老年代，而此时老年代空间不足造成的（有时候空间不足是CMS GC时当前的浮动垃圾过多导致暂时性的空间不足触发Full GC）。

而在 Parallel Scavenge 收集器下，默认是在要触发 Full GC前先执行一次 YGC，并且两次GC之间能让应用程序稍微运行一小下，以期降低 Full GC的暂停时间 (因为 YGC 会尽量清理了新生代的死对象，减少了 Full GC的工作量)，控制这个行为的VM参数是`-XX:+ScavengeBeforeFullGC`。

并发GC的触发条件就不一样，以 CMS GC为例，它主要是定时去检查老年代的使用量，但使用量超过了触发比例就会启动一次 CMS GC，对老年代做并发收集。