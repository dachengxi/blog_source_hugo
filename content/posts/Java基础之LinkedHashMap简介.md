---
title: Java基础之LinkedHashMap简介
date: 2016-05-25 20:24:23
categories: Java基础
---

LinkedHashMap知识点回顾学习。

<!--more-->

LinkedHashMap继承自HashMap，在HashMap的基础上增加了双向链表的功能。基于双向链表，LinkedHashMap能够保持元素的插入顺序，以及元素的访问顺序。

LinkedHashMap在HashMap的基础上增加了一条双向链表，能够保持元素的插入顺序，每次有新结点加入，都会加入到tail结点后面，然后tail结点移动到新的结点上。

另外LinkedHashMap还可以维护元素的访问顺序，当访问一个元素的时候，将这个元素移动到链表的尾部即可。可以基于这个功能来实现一个LRU策略的缓存。

# 源码

参照源码解析：[https://github.com/dachengxi/Java8u192SourceCode](https://github.com/dachengxi/Java8u192SourceCode)


# 参考
- 《数据结构（Java版）》