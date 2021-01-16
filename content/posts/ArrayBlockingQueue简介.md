---
title: ArrayBlockingQueue简介
date: 2016-05-29 18:46:59
categories: 
- Java基础
- 并发集合
tags:
- ArrayBlockingQueue
---

1. ArrayBlockingQueue基于数组，先进先出，从尾部插入到队列，从头部开始返回。
2. 线程安全的有序阻塞队列，内部通过“互斥锁”保护竞争资源。
3. 指定时间的阻塞读写
4. 容量可限制

<!-- more -->

# 定义
ArrayBlockingQueue继承AbstractQueue，实现了BlockingQueue，Serializable接口，内部元素使用Object[]数组保存。初始化时候需要指定容量`ArrayBlockingQueue(int capacity)`，ArrayBlockingQueue默认会使用非公平锁。

ArrayBlockingQueue只使用一把锁，造成在存取两种操作时会竞争同一把锁，而使得性能相对低下。

# add(E)方法和offer(E)

调用父类中的add方法，查看源码可知父类中的add方法是调用offer方法实现,所以查看offer方法源码，如下：

```
public boolean offer(E e) {
	//检查元素不为null
    checkNotNull(e);
    //加锁，独占锁保护竞态资源。
    final ReentrantLock lock = this.lock;
    lock.lock();
    try {
    	//队列已满，返回false
        if (count == items.length)
            return false;
        else {
        	//插入元素,返回true
            insert(e);
            return true;
        }
    } finally {
    	//释放锁
        lock.unlock();
    }
}
```

insert源码如下：

```
private void insert(E x) {
	//将元素添加到队列中
    items[putIndex] = x;
    //putIndex表示下一个被添加元素的索引，设置下一个被添加元素的索引，若队列满了，就设置下一个被添加元素索引为0
    putIndex = inc(putIndex);
    //队列的元素数加1
    ++count;
    //唤醒notEmpty上的等待线程,也就是取元素的线程。
    notEmpty.signal();
}
```

# take()方法

```
public E take() throws InterruptedException {
	//获取独占锁，加锁，线程是中断状态的话会抛异常
    final ReentrantLock lock = this.lock;
    lock.lockInterruptibly();
    try {
    	//队列为空，会一直等待
        while (count == 0)
            notEmpty.await();
        //取元素的方法
        return extract();
    } finally {
    	//释放锁
        lock.unlock();
    }
}
```

```
private E extract() {
    final Object[] items = this.items;
    E x = this.<E>cast(items[takeIndex]);
    //取完之后，删除元素
    items[takeIndex] = null;
    //设置下一个被取出的元素索引，若是最后一个元素，下一个被取出的元素索引为0
    takeIndex = inc(takeIndex);
    //元素数减1
    --count;
    //唤醒添加元素的线程
    notFull.signal();
    return x;
}
```

# 源码分析
>jdk1.7.0_71

```
//队列元素
final Object[] items;
//下次被take,poll,remove的索引
int takeIndex;
//下次被put,offer,add的索引
int putIndex;
//队列中元素的个数
int count;
//保护所有访问的主锁
final ReentrantLock lock;
//等待take锁,读线程条件
private final Condition notEmpty;
//等待put锁,写线程条件
private final Condition notFull;
```

## ArrayBlockingQueue(int capacity) 给定容量和默认的访问规则初始化
```
public ArrayBlockingQueue(int capacity){}
```

## ArrayBlockingQueue(int capacity, boolean fair)知道你跟容量和访问规则
```
//fair为true,在插入和删除时,线程的队列访问会阻塞,并且按照先进先出的顺序,false,访问顺序是不确定的
public ArrayBlockingQueue(int capacity, boolean fair) {
        if (capacity <= 0)
            throw new IllegalArgumentException();
        this.items = new Object[capacity];
        lock = new ReentrantLock(fair);
        notEmpty = lock.newCondition();
        notFull =  lock.newCondition();
    }
```

## ArrayBlockingQueue(int capacity, boolean fair,Collection<? extends E> c) 指定容量,访问规则,集合来初始化
```
public ArrayBlockingQueue(int capacity, boolean fair,
                              Collection<? extends E> c) {}
```

## add(E e) 添加元素到队列末尾,成功返回true,队列满了抛异常IllegalStateException
```
public boolean add(E e) {
        return super.add(e);
    }
```

## offer(E e)添加元素到队列末尾,成功返回true,队列满了返回false
```
public boolean offer(E e) {}
```

## put(E e) 添加元素到队列末尾,队列满了,等待.
```
public void put(E e) throws InterruptedException {}
```

## offer(E e, long timeout, TimeUnit unit)添加元素到队列末尾,如果队列满了,等待指定的时间
```
public boolean offer(E e, long timeout, TimeUnit unit){}
```

## poll() 移除队列头
```
public E poll() {}
```

## take() 移除队列头,队列为空的话就等待
```
public E take() throws InterruptedException {}
```

## poll(long timeout, TimeUnit unit)移除队列头,队列为空,等待指定的时间
```
public E poll(long timeout, TimeUnit unit) throws InterruptedException {}
```

## peek()返回队列头,不删除
```
public E peek() {}
```

## size()
```
public int size(){}
```

## remainingCapacity() 返回无阻塞情况下队列能接受容量的大小
```
public int remainingCapacity() {}
```

## remove(Object o)从队列中删除元素
```
public boolean remove(Object o) {}
```

## contains(Object o) 是否包含元素
```
public boolean contains(Object o) {}
```

## toArray()
```
public Object[] toArray(){}
```

## toArray(T[] a)
```
public <T> T[] toArray(T[] a) {}
```

## toString()
```
public String toString(){}
```

## clear()
```
public void clear(){}
```

## drainTo(Collection<? super E> c)移除队列中可用元素,添加到集合中
```
public int drainTo(Collection<? super E> c) {}
```

## drainTo(Collection<? super E> c, int maxElements)移除队列中给定数量的可用元素,添加到集合中
```
public int drainTo(Collection<? super E> c, int maxElements) {}
```

## iterator() 返回一个迭代器
```
public Iterator<E> iterator() {
        return new Itr();
    }
```





# 参考
[http://www.jianshu.com/p/9a652250e0d1](http://www.jianshu.com/p/9a652250e0d1)