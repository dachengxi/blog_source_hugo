---
title: JVM动态对象年龄判断
date: 2017-06-17 22:26:30
categories: 
- JVM
tags:
- JVM
---

JVM中新生代对象晋升到老年代的时候，对象会有个年龄最大阈值，但是除了这个年龄最大阈值外，JVM还会对对象年龄进行动态判断，不等到年龄最大阈值就可以晋升到老年代。

<!--more-->

有两个参数：`-XX:MaxTenuringThreshold`、`-XX:TargetSurvivorRatio`：

- `-XX:MaxTenuringThreshold`，晋升年龄最大阈值，默认15（年龄存在MarkWord中，4位，最大值是15），新生代中经过数次YGC后，对象仍然存活并且达到了晋升年龄阈值，该对象就会晋升到老年代中。
- ``-XX:TargetSurvivorRatio`，设置Suvivor区的目标使用率，默认50，表示Survivor区对象目标使用率为50%。

JVM虚拟机不总是要求对象年龄达到MaxTenuringThreshold指定的阈值才能晋升到老年代，而是会根据各个年龄段的对象的和占用Survivor空间的比率，来判断哪些年龄的的对象可以晋升到老年代。

# 动态年龄判断的原因

如果有很多对象的年龄都没达到MaxTenuringThreshold指定的阈值，这些对象就一直保留在Survivor区中，新对象进来可能就没办法移动到Survivor区，这些对象会进入老年代，如果触发了老年代GC，会把新生代存活的对象全部放入老年代，老年代对象增多容易造成Full GC。

# 动态年龄判断的计算

JVM会记录每个年龄段的对象的大小，将每个年龄段的大小从小到大累加，如果加入某个年龄段大小后的和超过了`(Survivor区大小 * TargetSurvivorRatio)/100`，也就是这些和超过了Suvivor容量的50%，就会取这个年龄和MaxTenuringThreshold两者中较小的那个值，从这个值往上的所有年龄段的对象晋升到老年代。

比如：Suvivor区大小10M，年龄段1大小3M，年龄段2大小2M，年龄段3大小3M，年龄段4大小1M，TargetSurvivorRatio设置为50，MaxTenuringThreshold设置为15，则`（年龄段1+年龄段2+年龄段3）> (10 * 50)/100`，此时得到的年龄段是3，3和15比较，取3，下次YGC时所有年龄段3和年龄段4的对象晋升到老年代。

# MaxTenuringThreshold值大小的影响

- 如果设置过大，有很多对象的年龄都没达到MaxTenuringThreshold指定的阈值，这些对象就一直保留在Survivor区中，新对象进来可能就没办法移动到Survivor区，这些对象会进入老年代，如果触发了老年代GC，会把新生代存活的对象全部放入老年代，老年代对象增多容易造成Full GC。
- 如果设置过小，大量短期对象都会晋升到老年代，频繁引起老年代GC。



