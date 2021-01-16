---
title: Vector简介
date: 2016-05-24 17:24:10
categories: Java基础
tags:
- vector
---

# Vector简介

1. Vector和ArrayList类似,基于Object数组方式实现
2. Vector是同步访问的,操作是线程安全的

<!-- more -->

# 源码分析
>jdk1.7.0_71

```
//保存Vector中的元素
protected Object[] elementData;
//Vector中存储的元素个数
protected int elementCount;
//Vector容量自动增长的大小,此数值小于等于0,容量增长为2倍
protected int capacityIncrement;
```

## Vector(int initialCapacity, int capacityIncrement) 初始容量和初始自动增长 构造

```
public Vector(int initialCapacity, int capacityIncrement){}
```

## Vector(int initialCapacity) 初始容量 ,自动增长2倍 构造

```
public Vector(int initialCapacity){}
```

## Vector() 初始容量10,自动增长2倍 构造

```
public Vector(){}
```

## Vector(Collection<? extends E> c) 使用集合初始化

```
public Vector(Collection<? extends E> c){}
```

## copyInto(Object[] anArray) 将Vector中的元素拷到Object数组中

```
public synchronized void copyInto(Object[] anArray) {}
```

## trimToSize() 

```
public synchronized void trimToSize() {} 
```

## ensureCapacity(int minCapacity) 增加容量

```
public synchronized void ensureCapacity(int minCapacity) {}
```

## grow(int minCapacity) 真正增长容量的方法

```
private void grow(int minCapacity) {}
```

## setSize(int newSize) 设置大小

```
public synchronized void setSize(int newSize) {}
```

## capacity() Vector的容量

```
public synchronized int capacity() {}
```

## size() Vector包含元素的数量

```
public synchronized int size() {}
```

## isEmpty()是否为空

```
public synchronized boolean isEmpty() {}
```

## elements()返回一个枚举

```
public Enumeration<E> elements() {
			return new Enumeration<E>() {
            int count = 0;

            public boolean hasMoreElements() {
                return count < elementCount;
            }

            public E nextElement() {
                synchronized (Vector.this) {
                    if (count < elementCount) {
                        return elementData(count++);
                    }
                }
                throw new NoSuchElementException("Vector Enumeration");
            }
        };
}
```

## contains(Object o) 是否包含指定元素

```
public boolean contains(Object o) {}
```

## indexOf(Object o) 返回第一个匹配的索引

```
public int indexOf(Object o) {}
```

## indexOf(Object o, int index) 返回第一个从index开始的匹配的Object

```
public synchronized int indexOf(Object o, int index){}
```

## lastIndexOf(Object o) 返回最后一个匹配的索引

```
public synchronized int lastIndexOf(Object o) {}
```

## lastIndexOf(Object o, int index)返回最后一个从index开始的匹配的Object

```
public synchronized int lastIndexOf(Object o, int index) {}
```

## elementAt(int index) 返回指定位置的元素

```
public synchronized E elementAt(int index) {}
```

## firstElement() 第一个元素

```
public synchronized E firstElement(){}
```

## lastElement() 最后一个元素

```
public synchronized E lastElement() {}
```

## setElementAt(E obj, int index) 设置指定位置的元素

```
public synchronized void setElementAt(E obj, int index) {}
```

## removeElementAt(int index) 移除指定位置的元素

```
public synchronized void removeElementAt(int index) {}
```
## insertElementAt(E obj,int index)指定位置之后插入元素

```
public synchronized void insertElementAt(E obj, int index) {}
```

## addElement(E obj) 添加元素到最后

```
public synchronized void addElement(E obj) {}
```

## removeElement(Object obj) 删除第一个匹配的元素

```
public synchronized boolean removeElement(Object obj) {}
```

## removeAllElements() 删除所有元素

```
public synchronized void removeAllElements() {}
```

## clone() 深拷贝

```
public synchronized Object clone() {}
```

## toArray() 返回Object数组

```
public synchronized Object[] toArray() {}
```

## toArray(T[] a) 返回指定类型的数组

```
public synchronized <T> T[] toArray(T[] a) {}
```

## elementData(int index) 返回指定位置的元素

```
E elementData(int index) {}
```

## get(int index) 获取指定位置的元素

```
public synchronized E get(int index) {}
```

## set(int index, E element) 替换指定位置的元素

```
public synchronized E set(int index, E element) {}
```

## add(E e) 添加元素到末尾

```
public synchronized boolean add(E e) {}
```

## remove(Object o) 删除第一个匹配的元素

```
public boolean remove(Object o) {}
```

## add(int index, E element) 指定位置后面插入元素

```
public void add(int index, E element){}
```

## remove(int index) 删除指定位置的元素

```
public synchronized E remove(int index) {}
```

## clear() 清空vector

```
public void clear() {}
```

## containsAll(Collection<?> c) 是否包含指定的集合

```
public synchronized boolean containsAll(Collection<?> c) {}
```

## addAll(Collection<? extends E> c) 把集合添加到vector的末尾

```
public synchronized boolean addAll(Collection<? extends E> c) {}
```
## removeAll(Collection<?> c) 删除vector中所有的指定集合中的元素

```
public synchronized boolean removeAll(Collection<?> c) {}
```

## retainAll(Collection<?> c)保留指定的集合元素,其他的删除

```
 public synchronized boolean retainAll(Collection<?> c) {}
```

## addAll(int index, Collection<? extends E> c) 添加指定的集合元素到指定的位置之后

```
public synchronized boolean addAll(int index, Collection<? extends E> c) {}
```

## equals(Object o)

```
public synchronized boolean equals(Object o) {}
```
## hashCode()

```
public synchronized int hashCode() {}
```

## toString()

```
public synchronized String toString() {}
```

## subList(int fromIndex, int toIndex)返回一个子list

```
public synchronized List<E> subList(int fromIndex, int toIndex) {}
```

## removeRange(int fromIndex, int toIndex) 删除指定区间的元素

```
protected synchronized void removeRange(int fromIndex, int toIndex) {}
```
## listIterator(int index)/listIterator() 返回一个ListIterator

```
public synchronized ListIterator<E> listIterator(int index) {}

public synchronized ListIterator<E> listIterator() {
        return new ListItr(0);
    }
```

## iterator() 返回一个Iterator

```
public synchronized Iterator<E> iterator() {
        return new Itr();
    }
```


# 参考

[http://www.cnblogs.com/skywang12345/p/3308833.html](http://www.cnblogs.com/skywang12345/p/3308833.html)

[http://www.runoob.com/java/java-vector-class.html](http://www.runoob.com/java/java-vector-class.html)