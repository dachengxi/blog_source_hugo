---
title: Lucene中的Field
date: 2018-06-17 15:10:17
categories: 
- Lucene
tags:
- Lucene
---

Lucene的Field学习。

<!--more-->

Field.Store枚举用来确定一个Field是否需要存储原始值，以便后续搜索时能恢复这个原始数据。

- YES，存储原始值，原始的字符串全部被保存在索引中，并且IndexReader可以恢复它。对于需要展示搜索结果的Field很有用，尽量不要存储Field太大的原始数据
- NO，不存储原始值，比如说一些正文信息等。



TODO

# 参考

- Lucene实战

