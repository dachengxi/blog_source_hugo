---
title: CopyOnWriteArraySet简介
date: 2016-05-27 17:20:34
categories: 
- Java基础
- 并发集合
tags:
- CopyOnWriteArraySet
---


1. 基于CopyOnWriteArrayList实现，线程安全无需集合。
2. add调用的是CopyOnWriteArraylist的addIfAbsent方法。
3. CopyOnWriteArraySet每次add要进行遍历数组,性能略低于CopyOnWriteArrayList。
4. 适用于set大小一般很小，读操作远远多于写操作的场景。

<!-- more -->

# 定义
CopyOnWriteArraySet集成AbstractSet，实现Serializable接口。是基于CopyOnWriteArrayList实现。

# add方法
通过CopyOnWriteArrayList的addIfAbsent实现。
基本方法都在CopyOnWriteArrayList中说明过，不做过多讲解。


# 源码分析
>jdk1.7.0_71

```
//基于CopyOnWriteArrayList
```

# 参考
