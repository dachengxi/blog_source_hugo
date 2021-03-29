---
title: Java基础之ConcurrentHashMap简介
date: 2016-05-26 11:00:23
categories: 
- Java基础
---

基于JDK1.8，重新梳理ConcurrentHashMap。

<!--more-->

JDK1.7中ConcurrentHashMap是使用分段锁来实现的，分段锁其实还是独占锁。而JDK1.8中ConcurrentHashMap的实现感觉起来更加轻量，使用cas+volatile+synchronized来保证并发安全，整体结构和HashMap类似，采用数组存储元素，冲突解决使用链表加红黑树的方式。

ConcurrentHashMap和HashMap的存储结构稍有不同，HashMap中数组存储的是Node类型结点，有冲突时，先是Node结点组成链表，转化为树的时候则是使用TreeNode类型结点。ConcurrentHashMap中数组存储的也是Node类型结点，冲突时先是Node结点组成链表，转化为树的时候有一点不同，树的根结点使用的是TreeBin，树的其他结点使用的是TreeNode。

# ConcurrentHashMap中的各个结点的类型

- Node，链表中使用的结点类型，结点的哈希值是正常的
- TreeNode，红黑树中使用的结点类型，结点的哈希值是正常的
- TreeBin，如果是红黑树，数组中存储的是TreeBin，TreeBin中持有红黑树，结点的哈希值是-2
- ForwardingNode，在数组扩容的时候使用，当在扩容的时候，如果一个桶位置处的元素已经全部转移完，则在桶中存放一个ForwardingNode，指向新数组，结点的哈希值是-1
- ReservationNode，占位符，在compute方法计算的时候使用，结点的哈希值是-3

# ConcurrentHashMap的get操作是怎么保证线程安全的？

ConcurrentHashMap中get方法没有使用锁，但却是线程安全的。当使用get方法读取ConcurrentHashMap的时候，其实是根据key的哈希值读取数组中某个桶位置处的元素，在数组中可能读到的元素有：

- Node，说明当前桶中是链表
- TreeBin，说明当前桶中是一颗红黑树
- ForwardingNode，说明当前桶中元素已经被转移到了新数组中的桶中
- ReservationNode，说明compute方法正在计算中

读到桶中是不同类型的结点时，也会有各种不同情况发生：

- 有线程正在写数据到这个桶中，ConcurrentHashMap写是多线程的，但是针对单个桶的写是单线程的，需要加锁。
- 数组可能正在扩容，当前槽正在被转移
- 如果桶中是链表，可能正在树化
- 如果桶中是红黑树，可能正在链表化

## 如果读到的桶中是个链表

如果读到的桶中是个链表，可能会有如下情况正在发生：

- 链表正在树化，链表树化会将Node转化为TreeNode，但是原来链表的结构没有变化，此时仍可以按照链表方式进行读取元素，没有线程安全问题。
- 链表正在被转移，扩容转移的时候不会破坏原来链表的结构，此时读取链表数据是没有线程安全问题的。
- 链表正在被写（put、remove、replace），由于写链表的时候会加锁，因此只有一个线程在写链表。put新元素的时候是在链表尾部进行，对读取没有影响；remove的时候结点的next指针没有变化，因此还可以继续遍历链表，没有影响；replace只是替换元素，不会改变链表结构，因此也不会产生线程安全问题。

## 如果读到的桶中是红黑树

如果读到的桶中是红黑树，可能有如下情况正在发生：

- 红黑树正在链表化，红黑树进行链表化的时候不会破坏红黑树的结构，因此读取红黑树不会有线程安全问题。
- 红黑树正在被转移，扩容转移的时候原来红黑树结构也不会被破坏，此时读取红黑树不会有线程安全问题。
- 红黑树正在被写（put、remove、replace），读取的时候正好红黑树被写，此时会先按链表进行遍历，虽然效率低，但是最起码能读取元素。每次遍历链表一个元素，都会检查下红黑树的写锁有没有被释放，如果被释放就转换成红黑树读取，效率高。树的查找也会加读锁。

## 如果读取的桶中是ForwardingNode

如果读取到的桶中是ForwardingNode，说明当前桶已经被转移完成了，ForwardingNode中有指向新数组的指针，直接到新数组中去读取。

## 如果读取的桶中是ReservationNode

如果读取的桶中是ReservationNode，说明compute方法正在计算，此时读取直接返回null。

# ConcurrentHashMap的写安全

ConcurrentHashMap写使用自旋+cas+synchronized来实现，将数据写入到某个桶的时候，会锁住该桶，只能有一个线程进行写，但是能并发进行读操作。

对于链表结构，单线程写链表尾部，不会有线程安全问题。

对于红黑树结构，本身会有简单的读写锁进行控制，也不会有线程安全问题。

# ConcurrentHashMap的扩容和转移机制

扩容的时候，从数组的最后开始往前处理，每个线程处理一个区间段的数组，使用transferIndex来控制每个线程要处理的区间段。一个线程处理一个区间段的数组时，回从后往前，挨个处理每个桶，处理每个桶的时候会加锁并转移数据。

转移数据的时候将数据分为两部分，一部分是结点哈希值和桶中链表长度取余为0的，这部分结点放到新的数组中的相同的桶中；另外一部分是取余为1的数据，这部分数据放到新数组的：原来桶索引+原数组长度的位置。

每个桶的数据转移完后，将桶中修改成ForwardingNode，用来指向新数组。

# ConcurrentHashMap的并发计数机制

ConcurrentHashMap的并发计数机制和LongAdder的实现机制一样，采用baseCount+一个计数数组实现，baseCount和计数数组中所有元素的和才是真正的size。

当没有竞争的时候，会先写baseCount，此时baseCount就是map的大小；当有竞争的时候，就会将每次新增大小累加到一个Cell数组中去。当有统计size大小的时候，就将baseCount和Cell数组中所有元素进行求和。

Cell元素是经过内存对齐的，每个Cell可以独占一个缓存行，这样可以防止伪共享的问题。

这样可以将对一个共享变量的读写转移到一个数组不同位置的读写，增加并发写的效率。

# 源码分析

参照源码解析：[https://github.com/dachengxi/Java8u192SourceCode](https://github.com/dachengxi/Java8u192SourceCode)

# 参考

- [https://my.oschina.net/qq785482254/blog/4307177](https://my.oschina.net/qq785482254/blog/4307177)
- [https://segmentfault.com/a/1190000024528036](https://segmentfault.com/a/1190000024528036)

