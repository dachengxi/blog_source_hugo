---
title: AtomicInteger简介
date: 2016-05-30 13:30:52
categories: 
- Java基础
- 并发包
tags:
- AtomicInteger
---

# AtomicInteger简介
1. 支持原子操作的Integer类
2. 主要用于在高并发环境下的高效程序处理。使用非阻塞算法来实现并发控制。

<!-- more -->

# 源码分析
>jdk1.7.0_71

```
//在更新操作时提供“比较并替换”的作用
private static final Unsafe unsafe = Unsafe.getUnsafe();
//用来记录value本身在内存的偏移地址
private static final long valueOffset;
//用来存储整数的时间变量，这里被声明为volatile，就是为了保证在更新操作时，当前线程可以拿到value最新的值
private volatile int value;
```

## AtomicInteger(int initialValue) 初始化
```
public AtomicInteger(int initialValue){}
```

## AtomicInteger()
```
public AtomicInteger(){}
```

## get()获取当前值
```
public final int get() {
        return value;
    }
```

## set(int value)设置值
```
public final void set(int newValue) {
        value = newValue;
    }
```

## lazySet(int newValue)
```
//lazySet延时设置变量值，这个等价于set()方法，但是由于字段是
//volatile类型的，因此次字段的修改会比普通字段（非volatile
//字段）有稍微的性能延时（尽管可以忽略），所以如果不是
//想立即读取设置的新值，允许在“后台”修改值，那么此方法就很有用。
public final void lazySet(int newValue) {
        unsafe.putOrderedInt(this, valueOffset, newValue);
    }
```

## getAndSet(int newValue)设定新数据,返回旧数据
```
public final int getAndSet(int newValue) {}
```

## compareAndSet(int expect,int update)比较并设置
```
public final boolean compareAndSet(int expect, int update) {
//使用unsafe的native方法，实现高效的硬件级别CAS
//native方法
return unsafe.compareAndSwapInt(this, valueOffset, expect, update);
    }
```

## weakCompareAndSet(int expect,int update)比较并设置
```
public final boolean weakCompareAndSet(int expect, int update) {
        return unsafe.compareAndSwapInt(this, valueOffset, expect, update);
    }
```

## getAndIncrement()以原子方式将当前值加 1，相当于线程安全的i++操作
```
public final int getAndIncrement() {
        for (;;) {
            int current = get();
            int next = current + 1;
            if (compareAndSet(current, next))
                return current;
        }
    }
```

## incrementAndGet( )以原子方式将当前值加 1， 相当于线程安全的++i操作。
```
public final int incrementAndGet() {}
```

## getAndDecrement( )以原子方式将当前值减 1， 相当于线程安全的i--操作。
```
public final int getAndDecrement() {}
```

## decrementAndGet ( )以原子方式将当前值减 1，相当于线程安全的--i操作。
```
public final int decrementAndGet() {}
``` 

## addAndGet( )： 以原子方式将给定值与当前值相加， 实际上就是等于线程安全的i =i+delta操作。
```
public final int addAndGet(int delta) {}
```

## getAndAdd( )：以原子方式将给定值与当前值相加， 相当于线程安全的t=i;i+=delta;return t;操作
```
public final int getAndAdd(int delta) {}
```





# 参考
[http://www.itzhai.com/the-introduction-and-use-of-atomicinteger.html#read-more](http://www.itzhai.com/the-introduction-and-use-of-atomicinteger.html#read-more)

[http://hittyt.iteye.com/blog/1130990](http://hittyt.iteye.com/blog/1130990)

[http://chenzehe.iteye.com/blog/1759884](http://chenzehe.iteye.com/blog/1759884)
