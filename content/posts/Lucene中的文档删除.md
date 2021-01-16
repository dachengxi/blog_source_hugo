---
title: Lucene中的文档删除
date: 2018-06-17 19:43:02
categories: 
- Lucene
tags:
- Lucene
---

Lucene中文档删除的更深入学习。

<!--more-->

# IndexReader删除文档

IndexReader和IndexWriter都可以删除文档：

- IndexReader能够根据文档号删除文档
- IndexReader可通过Term对象删除文档，IndexReader会返回被删除的文档号，IndexReader可以立即决定删除哪个文档；IndexWriter不会返回删除的文档号，因为它仅仅是将被删除的Term进行缓存，后续在进行实际的删除操作
- IndexReader删除操作会立即生效，IndexWriter的删除操作必须等到程序打开一个新的Reader时才能被感知
- IndexWriter可通过Query删除，IndexReader不行
- IndexReader的undeleteAll方法可以反向操作索引中所有被挂起的删除。只能对未进行段合并的文档进行反删除操作。之所以能这样，是因为IndexWriter只是将被删除文档标记为已删除状态，但并未真正移除这些文档，最终的删除操作是在该文档所对应段进行合并时才执行

# 回收被删除文档所使用的磁盘空间

Lucene使用bit数组的形式来标识文档被删除，操作很快，但是对应的磁盘空间仍占用。对于一个倒排索引，给定的文档项可能是分散在各处的，在删除文档时试图回收占用磁盘空间是不实际的，只有在发生段合并操作时，这些磁盘才能被回收。

也可以显式调用expungeDeletes方法来回收被删除文档占用的磁盘空间，该调用会对被挂起的删除操作相关的所有段进行合并

# 参考

- Lucene实战

