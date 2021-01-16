---
title: CopyOnWriteArrayList简介
date: 2016-05-25 10:30:09
categories: 
- Java基础
- 并发集合
tags:
- CopyOnWriteArrayList
---

1. CopyOnWriteArrayList，写数组的拷贝，支持高效率并发且是线程安全的,读操作无锁的ArrayList。所有可变操作都是通过对底层数组进行一次新的复制来实现。
2. CopyOnWriteArrayList适合使用在读操作远远大于写操作的场景里，比如缓存。它不存在扩容的概念，每次写操作都要复制一个副本，在副本的基础上修改后改变Array引用。CopyOnWriteArrayList中写操作需要大面积复制数组，所以性能肯定很差
3. 在迭代器上进行的元素更改操作（remove、set和add）不受支持。这些方法将抛出UnsupportedOperationException。

<!-- more -->

# 定义
CopyOnWriteArrayList跟ArrayList一样实现了List<E>, RandomAccess, Cloneable, Serializable接口，但是没有继承AbstractList。

初始化时候新建一个容量为0的数组。

# add(E e)方法

```
public boolean add(E e) {
	//获得锁，添加的时候首先进行锁定
    final ReentrantLock lock = this.lock;
    lock.lock();
    try {
    	//获取当前数组
        Object[] elements = getArray();
        //获取当前数组的长度
        int len = elements.length;
        //这个是重点，创建新数组，容量为旧数组长度加1，将旧数组拷贝到新数组中
        Object[] newElements = Arrays.copyOf(elements, len + 1);
        //要添加的数据添加到新数组的末尾
        newElements[len] = e;
        //将数组引用指向新数组，完成了添加元素操作
        setArray(newElements);
        return true;
    } finally {
    	//解锁
        lock.unlock();
    }
}
```

从上面来说，每次添加一个新元素都会长度加1，然后复制整个旧数组，由此可见对于写多的操作，效率肯定不会很好。所以CopyOnWriteArrayList适合读多写少的场景。

# add(int index, E element)方法

```
public void add(int index, E element) {
	//同样也是先加锁
    final ReentrantLock lock = this.lock;
    lock.lock();
    try {
    	//获取旧数组
        Object[] elements = getArray();
        //获取旧数组长度
        int len = elements.length;
        //校验指定的index
        if (index > len || index < 0)
            throw new IndexOutOfBoundsException("Index: "+index+
                                                ", Size: "+len);
        Object[] newElements;
        int numMoved = len - index;
        if (numMoved == 0)//需要插入的位置正好等于数组长度，数组长度加1，旧数据拷贝到新数组
            newElements = Arrays.copyOf(elements, len + 1);
        else {
        	//新数组长度增加1
            newElements = new Object[len + 1];
            //分两次拷贝，第一次拷贝旧数组0到index处的到新数组0到index，第二次拷贝旧数组index到最后的数组到新数组index+1到最后
            System.arraycopy(elements, 0, newElements, 0, index);
            System.arraycopy(elements, index, newElements, index + 1,
                             numMoved);
        }
        //index初插入数据
        newElements[index] = element;
        //新数组指向全局数组
        setArray(newElements);
    } finally {
    	//解锁
        lock.unlock();
    }
}
```

# set(int index, E element)方法

```
public E set(int index, E element) {
	//修改元素之前首先加锁
    final ReentrantLock lock = this.lock;
    lock.lock();
    try {
    	//获取原来的数组
        Object[] elements = getArray();
        //index位置的元素
        E oldValue = get(elements, index);
		//新旧值不相等才进行替换
        if (oldValue != element) {
        		//原来的长度
            int len = elements.length;
            //拷贝一份到新数组
            Object[] newElements = Arrays.copyOf(elements, len);
            //替换元素
            newElements[index] = element;
            //新数组指向全局数组
            setArray(newElements);
        } else {
            // Not quite a no-op; ensures volatile write semantics
            setArray(elements);
        }
        return oldValue;
    } finally {
    	//解锁
        lock.unlock();
    }
}
```

# get(int index)方法

读的时候不加锁，代码如下：

```
public E get(int index) {
    return get(getArray(), index);
}
```

```
private E get(Object[] a, int index) {
    return (E) a[index];
}
```

# remove（）
remove方法不再过多介绍，看完add和set方法应该就能理解。

# 迭代
内部类COWIterator 实现了ListIterator接口。迭代的时候不能进行remove，add，set等方法，会抛异常。

迭代速度快，迭代时是迭代的数组快照。

```
/** Snapshot of the array */
private final Object[] snapshot;
```

# 源码分析
>jdk1.7.0_71

```
//锁,保护所有存取器
transient final ReentrantLock lock = new ReentrantLock();
//保存数据的数组
private volatile transient Object[] array;
final Object[] getArray() {return array;}
final void setArray(Object[] a) {array = a;}
```
## 空构造,初始化一个长度为0的数组
```
public CopyOnWriteArrayList() {
        setArray(new Object[0]);
    }
```
## 利用集合初始化一个CopyOnWriteArrayList

```
public CopyOnWriteArrayList(Collection<? extends E> c) {}
```

## 利用数组初始化一个CopyOnWriteArrayList
```
public CopyOnWriteArrayList(E[] toCopyIn) {}
```

## size() 大小
```
public int size() {}
```

## isEmpty()是否为空
```
public boolean isEmpty(){}
```

## indexOf(Object o, Object[] elements,int index, int fence) 元素索引
```
private static int indexOf(Object o, Object[] elements,int index, int fence) {}
```

## indexOf() 元素索引
```
public int indexOf(Object o){}
```

## indexOf(E e, int index) 元素索引
```
public int indexOf(E e, int index) {}
```

## lastIndexOf(Object o, Object[] elements, int index) 元素索引,最后一个
```
private static int lastIndexOf(Object o, Object[] elements, int index) {}
```

## lastIndexOf(Object o) 元素索引,最后一个
```
public int indexOf(E e, int index) {}
```

## lastIndexOf(E e, int index) 元素索引,最后一个
```
public int lastIndexOf(E e, int index) {}
```



## contains(Object o) 是否包含元素
```
public boolean contains(Object o){}
```

## clone() 浅拷贝
```
public Object clone() {}
```

## toArray() 转换成数组
```
public Object[] toArray(){}
```

## toArray(T a[]) 转换成指定类型的数组
```
public <T> T[] toArray(T a[]) {}
```

## E get(int index)获取指定位置的元素
```
public E get(int index){}
```

## set(int index, E element) 指定位置设置元素
> 写元素的时候,先获得锁,finall块中释放锁

```
public E set(int index, E element) {
        final ReentrantLock lock = this.lock;
        lock.lock();
        try {
            Object[] elements = getArray();
            E oldValue = get(elements, index);

            if (oldValue != element) {
                int len = elements.length;
                Object[] newElements = Arrays.copyOf(elements, len);
                newElements[index] = element;
                setArray(newElements);
            } else {
                // Not quite a no-op; ensures volatile write semantics
                setArray(elements);
            }
            return oldValue;
        } finally {
            lock.unlock();
        }
    }
```

## add(E e) 元素添加到末尾
```
public boolean add(E e) {}
```

## add(int index, E element) 指定位置之后插入元素
```
public void add(int index, E element){}
```

## remove(int index)删除指定位置的元素
```
public E remove(int index) {}
```

## remove(Object o) 删除第一个匹配的元素
```
public boolean remove(Object o) {}
```

## removeRange(int fromIndex, int toIndex) 删除指定区间的元素
```
private void removeRange(int fromIndex, int toIndex) {}
```

## addIfAbsent(E e) 如果元素不存在就添加进list中
```
public boolean addIfAbsent(E e){}
```

## containsAll(Collection<?> c)是否包含全部
```
public boolean containsAll(Collection<?> c){}
```

## removeAll(Collection<?> c) 移除全部包含在集合中的元素
```
public boolean removeAll(Collection<?> c){}
```

## retainAll(Collection<?> c) 保留指定集合的元素,其他的删除
```
public boolean retainAll(Collection<?> c){}
```

## addAllAbsent(Collection<? extends E> c) 如果不存在就添加进去
```
public int addAllAbsent(Collection<? extends E> c) {}
```

## clear() 清空list
```
public void clear(){}
```

## addAll(Collection<? extends E> c)添加集合中的元素到尾部
```
public void addAll(Collection<? extends E> c){}
```

## addAll(int index, Collection<? extends E> c) 添加集合中元素到指定位置之后
```
public boolean addAll(int index, Collection<? extends E> c){}
```

## toString()
```
public String toString(){}
```

## equals(Object o)
```
public boolean equals(Object o) {
        if (o == this)
            return true;
        if (!(o instanceof List))
            return false;

        List<?> list = (List<?>)(o);
        Iterator<?> it = list.iterator();
        Object[] elements = getArray();
        int len = elements.length;
        for (int i = 0; i < len; ++i)
            if (!it.hasNext() || !eq(elements[i], it.next()))
                return false;
        if (it.hasNext())
            return false;
        return true;
    }
```

## hashCode()
```
public int hashCode{}
```

## listIterator(final int index)和 listIterator() 返回一个迭代器,支持向前和向后遍历

```
public ListIterator<E> listIterator(final int index) {}

public ListIterator<E> listIterator() {}
```

## iterator() 只能向后遍历

```
public Iterator<E> iterator() {}
```

## subList() 返回部分list

```
public List<E> subList(int fromIndex, int toIndex) {
	...
	return new COWSubList<E>(this, fromIndex, toIndex);
	...
}
```


# 参考
[http://www.cnblogs.com/sunwei2012/archive/2010/10/08/1845656.html](http://www.cnblogs.com/sunwei2012/archive/2010/10/08/1845656.html)

[http://my.oschina.net/jielucky/blog/167198](http://my.oschina.net/jielucky/blog/167198)