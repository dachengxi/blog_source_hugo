---
title: HashSet简介
date: 2016-05-27 16:35:17
categories: Java基础
tags:
- HashSet
---

# HashSet简介
1. HashSet是Set接口的实现,不允许元素重复
2. 元素不重复是基于HashMap实现
3. 非线程安全的
4. 不支持通过get(int)获取指定位置的元素,只能通过Iterator方法来获取

<!-- more -->

# 源码分析
>jdk1.7.0_71

```
//
```

## 空构造 实际是new 一个HashMap
```
public HashSet() {
        map = new HashMap<>();
    }
```

# 参考