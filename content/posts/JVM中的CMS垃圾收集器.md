---
title: JVM中的CMS垃圾收集器
date: 2017-07-01 17:04:04
categories: 
- JVM
tags:
- JVM
---

CMS是老年代的垃圾收集器，使用标记-清除算法，是并发的收集器。可与Serial收集器和Parallel New收集器配合使用。

<!--more-->

CMS尽可能的缩短垃圾收集时用户线程的停顿时间。分为四个阶段：

- 初始标记
- 并发标记
- 重新标记
- 并发清除

优点：

- 并发收集器
- GC过程中暂停时间短。

缺点：

- 对CPU资源敏感，并发阶段不会停顿，但是占用一部分县城，总吞吐量降低。
- 无法处理浮动垃圾，并发清理阶段，程序运行还会产生新的垃圾。
- CMS基于标记-清除算法，收集结束后会产生空间碎片。

# 触发CMS垃圾收集的条件

周期性的，JVM默认2秒触发一次CMS的GC；主动触发，YGC发生了Promotion Failed，会触发对老年代进行回收，或者System.gc()触发。

如果触发了主动的GC，周期性的GC会被夺取掉执行权，并记录 concurrent mode failure 或者 concurrent mode interrupted。

触发的条件：

- 如果没有设置`UseCMSInitiatingOccupancyOnly`参数，CMS默认会根据JVM运行时统计数据来判断是否触发CMS GC。
- 如果设置了`UseCMSInitiatingOccupancyOnly`参数，并且配置了`-XX:CMSInitiatingOccupancyFraction`参数，会根据这个配置的参数来判断老年代使用率是否超过阈值来决定是否开启CMS GC。
- 如果设置了`UseCMSInitiatingOccupancyOnly`参数，但没有配置`-XX:CMSInitiatingOccupancyFraction`参数，默认在使用率到92%的时候触发CMS GC。
- CMS默认不会对永久代进行垃圾收集，如果需要对永久代收集，需要设置参数`-XX:+CMSClassUnloadingEnabled`，CMS就会根据永久代内存使用率，默认92%，来判断是否开启CMS GC。
- 新生代晋升担保失败，会触发CMS GC。

# GC过程

可分为四个大的阶段：

- 初始标记
- 并发标记
- 重新标记
- 并发清除

按照GC日志中显示的，可以细分为7个阶段：

- 初始标记（CMS Initial Mark），会Stop The World
- 并发标记（CMS-concurrent-mark）
- 预清理（CMS-concurrent-preclean）
- 可被终止的预清理（CMS-concurrent-abortable-preclean）
- 重新标记（CMS Final Remark），会Stop The World
- 并发清除（CMS-concurrent-sweep）
- 重置状态（CMS-concurrent-reset），重置CMS实现的内部数据结构

## 初始标记

初始标记会Stop The World，标记GC Root能直接关联到的对象，速度很快。包含两部分：

- 标记GC Roots直接可达的老年代对象。
- 标记新生代中活着的对象引用到的老年代对象。

## 并发标记

并发标记阶段和应用线程兵法执行。遍历初始标记阶段的被标记的对象，继续递归标记这些对象的可达对象。

由于该阶运行的过程中用户线程也在运行，这就可能会发生这样的情况，已经被遍历过的对象的引用被用户线程改变等，如果发生了这样的情况，JVM就会标记这个区域为Dirty Card。后续只需要扫描这些Dirty Card，可以避免扫描整个老年代。

这个阶段是和用户线程并发的，可能会导致concurrent mode failure。

## 预清理

用来处理并发标记阶段的Dirty Card，遍历这些对象重新标记。

## 可终止的预清理

在预清理完成后，会判断Eden的占用量是否大于CMSScheduleRemarkEdenSizeThreshold(默认为2M)，如果大于就会启动当前可终止的预清理阶段，直到Eden区占用量达到CMSScheduleRemarkEdenPenetration(默认50%)，或达到5秒钟。但是如果ygc在这个阶段中没有发生的话，是达不到理想效果的。此时可以指定CMSMaxAbortablePrecleanTime，但是，等待一般都不是什么好的策略，可以采用CMSScavengeBeforeRemark，使remark之前发生一次YGC，从而减少remark阶段暂停的时间。

## 重新标记

重新标记阶段会Stop The World，该阶段的任务是完成标记整个年老代的所有的存活对象，尽管先前的pre clean阶段尽量应对处理了并发运行时用户线程改变的对象应用的标记，但是不可能跟上对象改变的速度，只是为final remark阶段尽量减少了负担。

## 并发清除

清除那些没有被标记的可以回收的对象，由于这一阶段应用程序线程也在运行，这时候产生的浮动垃圾就不能被处理，只能等到下一次GC时在清理。