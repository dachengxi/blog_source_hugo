---
title: LinkedList简介
date: 2016-05-23 23:20:51
categories: Java基础
tags:
- linkedList
---
# LinkedList简介

1. LinkedList基于双向链表实现
2. LinkedList相对于Arraylist来说,get和set等随机访问会比较慢,LinkedList需要移动指针；add和remove会比较快。
3. LinkedList类还为在列表开头和结尾的get，remove，insert元素提供统一的命名方法，这些操作允许将链表用做堆栈、队列或者双端队列。此类实现 Deque 接口，为 add、poll 提供先进先出队列操作，以及其他堆栈和双端队列操作。
4. 非线程安全，不同步。

<!--more-->

# 定义
LinkedList集成AbstractSequentialList，实现了List，Deque，Cloneable，Serializable接口。AbstractSequentialList提供了骨干实现。Deque一个线性 collection，支持在两端插入和移除元素，定义了双端队列的操作。

# 源码分析

>jdk1.7.0_71

```
//节点个数
transient int size = 0;
//前驱节点
transient Node<E> first;
//后继节点
transient Node<E> last;
```

## 无参构造

```
public LinkedList() {}
```

## 根据其他容器进行构造

```
public LinkedList(Collection<? extends E> c) {}
```

## getFirst()/getLast() 获取第一个/获取最后一个

```
public E getFirst() {}
public E getLast() {}
```

## removeFirst()/removeLast() 删除第一个/删除最后一个

```
public E removeFirst() {}
public E removeLast() {}
```

## addFirst(E e)/addLast(E e) 添加到头/尾

```
public void addFirst(E e) {}
public void addLast(E e) {}
```

## 是否包含指定的元素

```
public boolean contains(Object o) {
	return indexOf(o) != -1;
}
```

## 指定元素的位置索引

```
public int indexOf(Object o) {}
```

## 指定元素的最后的位置索引

```
public int lastIndexOf(Object o) {}
```

## list的大小
```
public int size() {
        return size;
}
```

## add() 添加到末尾

```
public boolean add(E e) {}
```

## remove(Object o)/removeFirstOccurrence(Object o) 移除第一个o

```
public boolean remove(Object o) {}

public boolean removeFirstOccurrence(Object o){}
```

## addAll(Collection<? extends E> c) 添加到list结尾

```
public boolean addAll(Collection<? extends E> c) {}
```

## addAll(int index, Collection<? extends E> c) 指定的位置以后添加全部 

```
public boolean addAll(int index, Collection<? extends E> c) {}
```

## clear() 清空

```
public void clear() {}
```

## get(int index) 获取指定位置的元素

```
public E get(int index) {}
```

## 指定位置替换成新元素,返回旧元素

```
public E set(int index, E element) {}
```

## add(int index, E element)指定位置插入指定元素

```
public void add(int index, E element) {}
```

## remove(int index) 移除指定位置的元素

```
public E remove(int index) {}
```

## peek()/peekFirst() 获取list头

```
public E peek() {}

public E peekFirst() {}
```

## element() 获取list头

```
public E element() {}
```

## poll()/pollFirst() 获取list头,并删除

```
public E poll() {}

public E pollFirst() {}
```

## remove() 删除第一个

```
public E remove() {}
```

## offer(E e)/offerLast(E e) 添加e到尾部

```
public boolean offer(E e) {}

public boolean offerLast(E e) {}
```

## offerFirst(E e)/push(E e) 添加e到头部

```
public boolean offerFirst(E e) {}

public void push(E e){}
```

## peekLast() 获取list尾

```
public E peekLast() {}
```

## pollLast() 获取list尾,并删除

```
public E pollLast
```

## pop() 删除头

```
public E pop(){}
```

## removeLastOccurrence(Object o)从后面开始删除第一个匹配的元素

```
public boolean removeLastOccurrence(Object o) {}
```

## listIterator(int index)从指定的位置开始返回一个listIterator

```
public ListIterator<E> listIterator(int index) {}
```

## descendingIterator() 逆序迭代器

```
public Iterator<E> descendingIterator() {
		//内部类
        return new DescendingIterator();
    }
```

## clone() 浅拷贝

```
public Object clone() {}
```

## toArray() 转换成Object数组

```
public Object[] toArray() {}
```

## toArray(T[] a) 转换成指定类型的数组

```
public <T> T[] toArray(T[] a) {
        if (a.length < size)
            a = (T[])java.lang.reflect.Array.newInstance(
                                a.getClass().getComponentType(), size);
        int i = 0;
        Object[] result = a;
        for (Node<E> x = first; x != null; x = x.next)
            result[i++] = x.item;

        if (a.length > size)
            a[size] = null;

        return a;
    }
```


# 参考

[https://www.zybuluo.com/pastqing/note/208830](https://www.zybuluo.com/pastqing/note/208830)

[http://blog.csdn.net/u013256816/article/details/50916689](http://blog.csdn.net/u013256816/article/details/50916689)