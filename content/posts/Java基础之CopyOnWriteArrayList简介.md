---
title: Java基础之CopyOnWriteArrayList简介
date: 2016-05-25 10:30:09
categories: 
- Java基础
- 并发集合
---

Java中CopyOnWrite机制的实现：CopyOnWriteArrayList学习。

<!--more-->

# CopyOnWrite机制

CopyOnWrite，写时复制，写时复制的思想，维基百科解释如下：

写入时复制（英语：Copy-on-write，简称COW）是一种计算机程序设计领域的优化策略。其核心思想是，如果有多个调用者（callers）同时请求相同资源（如内存或磁盘上的数据存储），他们会共同获取相同的指针指向相同的资源，直到某个调用者试图修改资源的内容时，系统才会真正复制一份专用副本（private copy）给该调用者，而其他调用者所见到的最初的资源仍然保持不变。这过程对其他的调用者都是透明的（transparently）。此做法主要的优点是如果调用者没有修改该资源，就不会有副本（private copy）被建立，因此多个调用者只是读取操作时可以共享同一份资源。

CopyOnWrite的应用场景：

- Linux系统的CopyOnWrite机制
- 文件系统的CopyOnWrite机制
- Java中的CopyOnWrite机制

写时复制机制，读的时候，使用同一个副本，写的时候需要复制一份副本出来，写完新副本后，将原来对旧副本的引用指向新的副本。在引用更改之前，所有的读还是在原来的副本上。引用更改之后，所有的读都是在新的副本上写时复制也是一种读写分离的思想，读和写在不同的副本上，并发读不受写时加锁的影响。

## Linux系统的CopyOnWrite机制

Linux中，fork会产生一个和父进程完全相同的子进程，两个进程使用相同的物理空间，子进程的代码段、数据段、堆栈都是指向父进程的物理空间。两者的虚拟空间不同，但对应的物理空间是同一个。

如果发生了exec系统调用，内核会给子进程代码段、数据段、堆栈分配相应的物理空间。如果是非exec系统调用，内核会给子进程数据段、堆栈分配物流空间，代码段还是和父进程共享。

### Linux的CopyOnWrite技术实现原理

fork()之后，kernel把父进程中所有的内存页的权限都设为read-only，然后子进程的地址空间指向父进程。当父子进程都只读内存时，相安无事。当其中某个进程写内存时，CPU硬件检测到内存页是read-only的，于是触发页异常中断（page-fault），陷入kernel的一个中断例程。中断例程中，kernel就会把触发的异常的页复制一份，于是父子进程各自持有独立的一份。

### Linux的CopyOnWrite机制的好处

- 可以减少分配和复制大量资源时带来的瞬间延时
- 可以减少不必要的资源分配，fork进程时不是所有的页面都需要复制

## Redis关于哈希表扩容时有关CopyOnWrite的说明

执行BGSAVE命令或者BGREWRITEAOF命令的过程中，Redis需要创建当前服务器进程的子进程，而大多数操作系统都采用写时复制（copy-on-write）来优化子进程的使用效率，所以在子进程存在期间，服务器会提高负载因子的阈值，从而避免在子进程存在期间进行哈希表扩展操作，避免不必要的内存写入操作，最大限度地节约内存。

对上面一段的解释是：

执行BGSAVE命令或者BGREWRITEAOF的时候，会fork一个子进程用来读取数据，在哈希表扩容的时候，会涉及到很多的写操作，如果在上面操作进行的时候发生了哈希扩容，就会因为Linux的CopyOnWrite机制，导致大量的进程数据的拷贝，影响性能。因此Redis在fork出子进程后，就将负载因子提高，减少扩容操作导致的写操作。

## 文件系统的CopyOnWrite机制

写文件的时候复制一份副本出来，写完副本后，把副本文件变成正式文件，这样在系统断电之后不用通过文件日志回滚数据到掉电之前的位置，老的文件还是没有变化的，保证了数据的完整新，恢复操作简单。

## Java的CopyOnWrite机制

CopyOnWriteArrayList是ArrayList的线程安全版本，使用CopyOnWrite机制，读的时候读取同一个数组，发生写操作的时候复制一个数组出来进行操作，读操作还是发生在老的数组上，等到写操作完成后，将指向原来数组的引用指向新的数组，后续读操作就是新的数组了。

### 缺点

- 会有数据一致性问题，CopyOnWrite只能保证最终的一致性，不能保证实时一致性。写的时候，读操作还是在原来的数组上，写操作完成后读操作就会到新的数组上。
- 还会有内存占用的问题，写操作的时候会复制一份副本，导致内存占用，对象过大可能会发生GC。

### 适用场景

读多写少，读的速度要求快，写的速度慢也没关系，比如一些配置信息、黑名单

# 定义

CopyOnWriteArrayList跟ArrayList一样实现了List<E>, RandomAccess, Cloneable, Serializable接口，但是没有继承AbstractList。

初始化时候新建一个容量为0的数组。

# add(E e)方法

```
public boolean add(E e) {
	// 获得锁，添加的时候首先进行锁定
    final ReentrantLock lock = this.lock;
    lock.lock();
    try {
    	// 获取当前数组
        Object[] elements = getArray();
        // 获取当前数组的长度
        int len = elements.length;
        // 这个是重点，创建新数组，容量为旧数组长度加1，将旧数组拷贝到新数组中
        Object[] newElements = Arrays.copyOf(elements, len + 1);
        // 要添加的数据添加到新数组的末尾
        newElements[len] = e;
        // 将数组引用指向新数组，完成了添加元素操作
        setArray(newElements);
        return true;
    } finally {
    	// 解锁
        lock.unlock();
    }
}
```

从上面来说，每次添加一个新元素都会长度加1，然后复制整个旧数组，由此可见对于写多的操作，效率肯定不会很好。所以CopyOnWriteArrayList适合读多写少的场景。

# add(int index, E element)方法

```
public void add(int index, E element) {
	// 同样也是先加锁
    final ReentrantLock lock = this.lock;
    lock.lock();
    try {
    	// 获取旧数组
        Object[] elements = getArray();
        // 获取旧数组长度
        int len = elements.length;
        // 校验指定的index
        if (index > len || index < 0)
            throw new IndexOutOfBoundsException("Index: "+index+
                                                ", Size: "+len);
        Object[] newElements;
        int numMoved = len - index;
        if (numMoved == 0)// 需要插入的位置正好等于数组长度，数组长度加1，旧数据拷贝到新数组
            newElements = Arrays.copyOf(elements, len + 1);
        else {
        	// 新数组长度增加1
            newElements = new Object[len + 1];
            // 分两次拷贝，第一次拷贝旧数组0到index处的到新数组0到index，第二次拷贝旧数组index到最后的数组到新数组index+1到最后
            System.arraycopy(elements, 0, newElements, 0, index);
            System.arraycopy(elements, index, newElements, index + 1,
                             numMoved);
        }
        // index初插入数据
        newElements[index] = element;
        // 新数组指向全局数组
        setArray(newElements);
    } finally {
    	// 解锁
        lock.unlock();
    }
}
```

# set(int index, E element)方法

```
public E set(int index, E element) {
	// 修改元素之前首先加锁
    final ReentrantLock lock = this.lock;
    lock.lock();
    try {
    	// 获取原来的数组
        Object[] elements = getArray();
        // index位置的元素
        E oldValue = get(elements, index);
		// 新旧值不相等才进行替换
        if (oldValue != element) {
        		// 原来的长度
            int len = elements.length;
            // 拷贝一份到新数组
            Object[] newElements = Arrays.copyOf(elements, len);
            // 替换元素
            newElements[index] = element;
            // 新数组指向全局数组
            setArray(newElements);
        } else {
            //  Not quite a no-op; ensures volatile write semantics
            setArray(elements);
        }
        return oldValue;
    } finally {
    	// 解锁
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
// 锁,保护所有存取器
transient final ReentrantLock lock = new ReentrantLock();
// 保存数据的数组
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
                //  Not quite a no-op; ensures volatile write semantics
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

- [https://www.cnblogs.com/Java3y/p/9884583.html](https://www.cnblogs.com/Java3y/p/9884583.html)
- [https://www.cnblogs.com/biyeymyhjob/archive/2012/07/20/2601655.html](https://www.cnblogs.com/biyeymyhjob/archive/2012/07/20/2601655.html)
- [http://ifeve.com/java-copy-on-write/](http://ifeve.com/java-copy-on-write/)