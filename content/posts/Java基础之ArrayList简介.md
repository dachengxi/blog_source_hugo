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

线性表的顺序存储结构（我们第一个想到的可能就是数组，其实线性表也是使用一维数组来存储），用一组连续的内存单元依次存放数据元素，元素在物理内存中的存储顺序也反映了线性表元素之间的逻辑顺序。数组是一个随机存取结构，获取元素的时间复杂度是O(1)，线性表和数组一样也是随机存取结构。

### 线性表的链式存储结构

链式存储结构是使用地址分散的存储单元存储数据元素。和顺序存储结构不同，链式存储结构的元素在逻辑位置上相邻但是在物理位置上可能不相邻。这种特殊的存储结构由于物理存储位置可能不相邻，所以在存储数据的时候，也需要存储额外的数据信息来表示元素之间的顺序关系。链式存储结构中存储一个数据元素的存储单元称为结点（Node）

## 线性表的操作

线性表基本操作一般有：

- 插入
- 删除
- 查找
- 替换

也不难理解，我们可以把一个线性表比作是数据库表，用来存储一些数据在里面，对表的操作就是增删改查。

### 顺序表的操作

顺序表的存储结构和数组一样，可以根据数组的特点来分析顺序表各种操作的特点：

- 顺序表的查询获取元素的效率很高，因为它是随机存取结构的，随机存取操作的时间复杂度是O(1)，对于顺序查找，由于要遍历顺序表，所以时间复杂度是O(N)，遍历的效率也是很高的。
- 顺序表的插入和删除操作效率很低，为了维持顺序表结构，插入和删除都需要移动元素。另外由于顺序表容量不能更改，如果有元素溢出的话，需要进行扩容，扩容的实现就是重新申请一个容量更大的顺序表，并将元素复制到新的顺序表中。

### 链表的操作

链表的存储结构不是随机存取结构，操作的特点和顺序表不一样，链表操作的特点如下：

- 链表的查询获取元素，需要从表头开始对链表进行遍历，时间复杂度是O(N)，相比顺序存储结构效率会差一点点。
- 顺序表的插入和删除操作，需要先定位元素位置，也需要对链表进行遍历，因此时间复杂度也是O(N)。而由于链表插入和删除操作仅需要操作结点的指针，不需要对数据进行移动、复制等操作，效率是高于顺序存储结构的。

# ArrayList简介

- ArrayList的内部实际是使用数组进行实现，可认为是线性表的顺序存储结构，所以ArrayList拥有线性表的优点以及缺点。比如支持随机存取，查询速度快。插入和删除涉及数据的移动和复制，效率会比较低。
- ArrayList和数组不同的是ArrayList可以进行动态扩容，实现也是线性表的定义中的实现：新生成一个数组，将原有数据拷贝到新的数组中去。
- ArrayList不是线程安全的，使用时应注意。

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