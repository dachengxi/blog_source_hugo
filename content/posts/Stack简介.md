---
title: Stack简介
date: 2016-05-25 10:20:44
categories: Java基础
tags:
- Stack
---

# Stack简介

1. Stack基于Vector实现,支持LIFO 后进先出

<!-- more -->
# 源码分析
>jdk1.7.0_71

## 默认构造

```
public Stack() {
    }
```

## push(E item)将元素压入顶端

```
public E push(E item) {
        addElement(item);

        return item;
    }
```

## pop() 删除顶部的元素,同步方法

```
public synchronized E pop() {
        E       obj;
        int     len = size();

        obj = peek();
        removeElementAt(len - 1);

        return obj;
    }
```

## peek() 获取顶端元素

```
public synchronized E peek() {
        int     len = size();

        if (len == 0)
            throw new EmptyStackException();
        return elementAt(len - 1);
    }
```

## empty() 是否为空

```
public boolean empty(){}
```

## search(Object o) 查询当前o距离栈底的距离

```
public synchronized int search(Object o) {
        int i = lastIndexOf(o);

        if (i >= 0) {
            return size() - i;
        }
        return -1;
    }
```


# 参考
