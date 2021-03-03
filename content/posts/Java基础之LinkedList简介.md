---
title: Java基础之LinkedList简介
date: 2016-05-23 23:20:51
categories: Java基础
---

LinkedList可以结合数据结构中线性表的链式存储结构来理解。

# 数据结构中的线性表链式存储结构

线性表的链式存储结构，使用地址分散的存储单元来存储数据元素，逻辑上相邻的数据元素，在物理位置上不一定相邻。需要使用附加信息来存储元素之间的顺序关系，存储数据元素的存储单元称为结点，结点包括：数据域和地址域。

链表可以分为两大类：

- 单链表，每个结点只有一个地址域的线性链表
- 双链表，每个结点有两个地址域，分别指向前驱结点和后继结点

链表中的结点空间是在插入和删除过程中动态申请和释放的，无需预先分配存储空间，可以避免顺序表的扩容和复制元素操作，提高效率和存储空间利用率。链表不是随机存取结构。

单链表可以分为：

- 不带头结点的单链表
- 带头结点的单链表，插入和删除操作不需要区分操作位置；表头指针head非空，实现共享单链表
- 排序单链表
- 循环单链表，最后一个结点的next指向head，形成环形结构

双链表可以分为：

- 双链表
- 循环双链表，最后一个结点的next指向头结点，头结点的prev指向最后一个结点
- 排序循环双链表


# LinkedList简介

基于数据结构中的线性表的链式存储接口来理解LinkedList会更容易：

1. LinkedList基于双向链表实现，节点中有两个地址域，分别指向前驱结点和后继结点
2. LinkedList是链式存储结构，非随机访问结构，相对于Arraylist来说，get和set等随机访问会比较慢，LinkedList需要遍历链表；add和remove一个结点会比较快，但是如果涉及到节点查找，还是需要遍历链表。
3. LinkedList还可以用做堆栈、队列或者双端队列。
4. 非线程安全。

<!--more-->

# 定义

LinkedList集成AbstractSequentialList，实现了List，Deque，Cloneable，Serializable接口。AbstractSequentialList提供了骨干实现。Deque一个线性 collection，支持在两端插入和移除元素，定义了双端队列的操作。

# 源码分析

可以参考下Github上的源码解析：[Java8u192SourceCode](https://github.com/dachengxi/Java8u192SourceCode.git)


# 参考

- 《数据结构（Java版）》