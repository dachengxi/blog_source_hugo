---
title: JVM空间分配担保
date: 2017-07-01 23:12:17
categories: 
- JVM
tags:
- JVM
---

当发生YGC时，JVM先检查老年代最大的可用连续空间是否大于新生代所有对象总和，如果大于，这次YGC是安全的；如果不大于则会看HandlePromotionFailure参数配置的值。

<!--more-->

HandlePromotionFailure为true表示允许担保失败，JVM会继续检查老年代最大可用连续空间是否大于历次晋升到老年代的对象的平均大小，如果大于，则尝试进行一次YGC，但这次YGC是有风险的；如果小于则进行一次Full GC。

HandlePromotionFailure为false表示不允许担保失败，JVM会进行一次Full GC。

上面说的YGC是有风险的解释：

新生代使用复制算法，如果出现大量对象在YGC之后都存活，Suvivor放不下这些对象，就需要空间分配担保，把这些对象放进老年代。老年代如果根据历次晋升到老年代的对象的平均大小来做判断，并不一定会准确，有可能会出现担保失败，出现担保失败就会触发一次Full GC。虽然有这种概率出现，但是大部分情况都能分配担保成功，可以避免频繁Full GC。