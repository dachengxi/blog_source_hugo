---
title: Lucene中的Segment合并
date: 2018-06-18 09:51:10
categories: 
- Lucene
tags:
- Lucene
---

Lucene中的Segment合并学习。

<!--more-->

如果索引包含Segment太多，IndexWriter会选择其中的一些，将它们合并成一个Segment：

- 合并后会减少索引中Segment的数量，完成合并后，旧的Segment会被删除。搜索的Segment减少，能加快搜索速度；避免达到操作系统的文件描述打开过多的限制
- 合并还会减小索引尺寸，如果被合并的Segment中包含挂起的删除操作，合并会释放删除数据占用的空间

# Segment合并策略

MergePolicy，合并策略的抽象。IndexWriter依赖MergePolicy具体的子类实现来完成合并：

- LogByteSizeMergePolicy，默认策略，会测量Segment的尺寸，就是Segment包含的所有文件总字节数
- LogDocMergePolicy，测量是通过Segment中文档数量来表示

一旦索引级别数达到或超过mergeFactor设定的尺寸，Segment将被合并。

# MergeScheduler

选取好了需要合并的Segment后，接下来就是真正的合并操作，IndexWriter使用MergeScheduler的子类实现来完成。默认使用ConcurrentMergeScheduler进行，利用后台线程完成合并。SerialMergeScheduler由调用它的线程来完成合并。

# 参考

- Lucene实战

