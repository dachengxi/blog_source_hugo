---
title: Java基础之ArrayList简介
date: 2016-05-20 16:47:46
categories: Java基础
---
借着ArrayList的学习，重温一下数据结构中的表（线性表）的相关知识，并从数据结构的基础知识开始，一点点进入ArrayList的学习。

<!--more-->

# 数据结构中的线性表介绍

数据结构中最基本的数据结构：表（List）、栈（Stack）、队列（Queue），这些定义和实现如果能够充分掌握好，学习Java中的对应实现问题就不会很多。

## 线性表的定义

线性表，从名字中就能看出来其含义，说的是组成表的元素之间具有线性关系。它是由有限个类型相同的元素组成的序列。

## 线性表的分类

线性表的实现可以分为两种：

- 顺序表类
- 链表类

### 线性表的顺序存储结构

线性表的顺序存储结构（我们第一个想到的可能就是数组），用一组连续的内存单元依次存放数据元素。

## 线性表的操作

线性表基本操作一般有：

- 插入
- 删除
- 查找
- 替换

也不难理解，我们可以把一个线性表比作是数据库表，用来存储一些数据在里面，对表的操作就是增删改查。

# ArrayList简介

ArrayList实现了List接口，内部以数组存储数据，允许重复的值。由于内部是数组实现，所以ArrayList具有数组所有的特性，通过索引支持随机访问，查询速度快，但是插入和删除的效率比较低。

ArrayList默认初始容量为10，每次添加新元素时都会检查是否需要扩容操作。扩容操作需要重新拷贝数组，比较耗时，所以如果预先能知道数组的大小，在初始化时候可以指定一个初始容量。

ArrayList不是线程安全的，使用时应注意。



# 源码分析
>jdk1.7.0_71

```
//默认容量
private static final int DEFAULT_CAPACITY = 10;
//默认空的数组,定义空的ArrayList
private static final Object[] EMPTY_ELEMENTDATA = {};
//保存ArrayList中元素的数组，以下基本操作都是基于该数组
private transient Object[] elementData;
//ArrayList中元素数组的容量
private int size;
```



## 无参构造函数

```
	//构造一个空的Object数组，初始容量为10
    public ArrayList() {}
```

## 指定容量大小的构造函数

```
//指定初始容量
public ArrayList(int initialCapacity) {}
```

## 指定初始值为继承Collection接口的集合的构造函数

```
public ArrayList(Collection<? extends E> c) {}
```

## trimToSize

```
//ArrayList每次增长会预申请1.5倍+1的空间,
//举个例子就是当size() = 1000的时候，ArrayList已经申请了1200空间
//trimToSize 的作用是删除多余的200
public void trimToSize() {
        modCount++;
		...
    }
```

## 关于modCount

>modCount定义在抽象类AbstratList中， 源码的注释基本说明了它的用处:在使用迭代器遍历的时候，用来检查列表中的元素是否发生结构性变化（列表元素数量发生改变的一个计数）了，主要在多线程环境下需要使用，防止一个线程正在迭代遍历，另一个线程修改了这个列表的结构。

## ensureCapacity

```
//增加此ArrayList实例的容量，如果需要，以确保其能容纳至少由最小容量参数指定的元素数
public void ensureCapacity(int minCapacity) {}
```

## grow()方法,实际的扩容方法,扩充为原来的1.5倍

```
//数组最大值
private static final int MAX_ARRAY_SIZE = Integer.MAX_VALUE - 8;

private void grow(int minCapacity) {
		// overflow-conscious code
        int oldCapacity = elementData.length;
        int newCapacity = oldCapacity + (oldCapacity >> 1);
        if (newCapacity - minCapacity < 0)
            newCapacity = minCapacity;
        if (newCapacity - MAX_ARRAY_SIZE > 0)
            newCapacity = hugeCapacity(minCapacity);
        // minCapacity is usually close to size, so this is a win:
        elementData = Arrays.copyOf(elementData, newCapacity);
}
```

## size() ArrayList的大小

```
public int size() {}
```

## isEmpty() 不包含元素,返回true

```
public boolean isEmpty() {}
```

## contains()包含指定的元素,返回true

```
public boolean contains(Object o) {}
```

## indexOf()

```
public int indexOf(Object o) {}
```

## lastIndexOf()

```
public int lastIndexOf(Object o) {}
```

## clone() 浅拷贝

```
public Object clone() {}
```

## toArray() 返回Object数组

```
public Object[] toArray() {}
```

## toArray(T[] a)

```
//返回数组的运行时类型是指定数组的。如果列表中指定的数组能容纳，则在其中返回。
//否则，一个新的数组分配具有指定数组的运行时类型和此列表的大小。
//如果列表中指定的数组能容纳更加节省空间(即数组的元素比列表元素多)，
//那么会将紧挨着collection尾部的元素设置为null。

public <T> T[] toArray(T[] a) {}
```

## elementData() 根据索引返回数据

```
E elementData(int index) {}
```

## get(int index) 根据索引获取

```
public E get(int index) {
        rangeCheck(index);//范围检查,看是否给定的index是否超出数组大小
		...
    }
```

## set(int index, E element) 根据索引替换

```
public E set(int index, E element) {
        rangeCheck(index);
		...
    }
```

## add(E e) 添加元素到元素末尾

```
public boolean add(E e) {
        ensureCapacityInternal(size + 1);  // Increments modCount!!
		...
    }
```

## add(int index,E element) 添加元素到指定位置

```
public void add(int index, E element) {
        rangeCheckForAdd(index);
		...
    }
```

## remove(int index)删除指定位置的元素

```
public E remove(int index) {
		...
		elementData[--size] = null; // clear to let GC do its work
		...
}
```

## remove(Object o) 删除指定元素

```
public boolean remove(Object o) {
        ...
        fastRemove(index);
        ...
    }
```

## fastRemove(int index)快速删除,不检查边界,无返回值

```
private void fastRemove(int index) {}
```

## clear() 清空

```
public void clear() {
        modCount++;

        // clear to let GC do its work
        for (int i = 0; i < size; i++)
            elementData[i] = null;

        size = 0;
    }
```

## addAll(Collection<? extends E> c) 添加指定的集合到末尾

```
public boolean addAll(Collection<? extends E> c) {}
```

## addAll(int index, Collection<? extends E> c) 添加指定元素到指定的位置

```
public boolean addAll(int index, Collection<? extends E> c) {}
```

## removeRange(int fromIndex, int toIndex) 删除指定区间的元素

```
protected void removeRange(int fromIndex, int toIndex) {}
```

## removeAll(Collection<?> c) 删除指定的集合元素

```
public boolean removeAll(Collection<?> c) {
        return batchRemove(c, false);
    }
```

## retainAll(Collection<?> c) 保留指定集合元素

```
public boolean retainAll(Collection<?> c) {
        return batchRemove(c, true);
    }
```

## listIterator(int index)和 listIterator() 返回一个迭代器,支持向前和向后遍历

```
public ListIterator<E> listIterator(int index) {}

public ListIterator<E> listIterator() {}
```

## iterator() 只能向后遍历

```
public Iterator<E> iterator() {}
```

## subList() 返回部分list

```
public List<E> subList(int fromIndex, int toIndex) {}
```

# 参考内容

- 《数据结构（Java版）》
- [http://blog.csdn.net/crave_shy/article/details/17436773](http://blog.csdn.net/crave_shy/article/details/17436773)

- [http://www.importnew.com/19233.html](http://www.importnew.com/19233.html)

- [https://www.zybuluo.com/pastqing/note/200073](https://www.zybuluo.com/pastqing/note/200073)

- [http://www.cnblogs.com/sipher/articles/2429812.html](http://www.cnblogs.com/sipher/articles/2429812.html)