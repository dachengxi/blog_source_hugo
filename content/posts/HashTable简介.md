---
title: HashTable简介
date: 2016-05-26 10:35:01
categories: Java基础
tags:
- HashTable
---

> HashTable继承Dictionary类，实现Map接口。其中Dictionary类是任何可将键映射到相应值的类（如 Hashtable）的抽象父类。每个键和每个值都是一个对象。在任何一个 Dictionary 对象中，每个键至多与一个值相关联。Map是"key-value键值对"接口。


<!-- more -->

# HashTable与HashMap的区别
1. HashTable基于Dictionary类，而HashMap是基于AbstractMap。Dictionary是什么？它是任何可将键映射到相应值的类的抽象父类，而AbstractMap是基于Map接口的骨干实现，它以最大限度地减少实现此接口所需的工作。
2. HashMap可以允许存在一个为null的key和任意个为null的value，但是HashTable中的key和value都不允许为null。当HashMap遇到为null的key时，它会调用putForNullKey方法来进行处理。对于value没有进行任何处理，只要是对象都可以。而当HashTable遇到null时，他会直接抛出NullPointerException异常信息。
3. Hashtable的方法是同步的，而HashMap的方法不是。所以有人一般都建议如果是涉及到多线程同步时采用HashTable，没有涉及就采用HashMap。

# 源码分析
>jdk1.7.0_71

```
//用于存储数据的表
private transient Entry<K,V>[] table;
//表中键值对的数
private transient int count;
//下次扩充的临界值 capacity * loadFactor
private int threshold;
//哈希表的负载因子
private float loadFactor;
//在使用迭代器遍历的时候，用来检查列表中的元素是否发生结构性变化（列表元素数量发生改变的一个计数）了，主要在多线程环境下需要使用，防止一个线程正在迭代遍历，另一个线程修改了这个列表的结构。
private transient int modCount;
//容量阈值，默认大小为Integer.MAX_VALUE
static final int ALTERNATIVE_HASHING_THRESHOLD_DEFAULT = Integer.MAX_VALUE;

```

## Holder 静态内部类,存放一些在虚拟机启动后才能初始化的值

### 容量阈值，初始化hashSeed的时候会用到该值 

```
static final int ALTERNATIVE_HASHING_THRESHOLD;
```

### static静态块
```
获取系统变量jdk.map.althashing.threshold
jdk.map.althashing.threshold系统变量默认为-1，如果为-1，则将阈值设为Integer.MAX_VALUE
```

## Hashtable(int initialCapacity, float loadFactor) 指定容量和负载因子 构造
```
public Hashtable(int initialCapacity, float loadFactor) {
	...
	initHashSeedAsNeeded();
}
```

## Hashtable(int initialCapacity) 指定初始容量的构造,负载因子为0.75f
```
public Hashtable(int initialCapacity) {}
```

## Hashtable() 默认初始容量11和默认负载因子0.75f的构造
```
public Hashtable(){}
```

## Hashtable(Map<? extends K, ? extends V> m) 用map初始化
```
public Hashtable(Map<? extends K, ? extends V> m) {
	this(Math.max(2*t.size(), 11), 0.75f);
		//把元素放入到Hashtable中
        putAll(t);
}
```

## size() key-value映射个数
```
public synchronized int size() {
        return size;
    }
```

## isEmpty()是否为空
```
public synchronized boolean isEmpty() {
        return size == 0;
    }
```

## keys() 返回keys枚举
```
public synchronized Enumeration<K> keys() {
        return this.<K>getEnumeration(KEYS);
    }
```

## elements() 返回values枚举
```
public synchronized elements<V> keys() {
        return this.<V>getEnumeration(VALUES);
    }
```

## contains(Object value)是否包含指定value
```
public synchronized boolean contains(Object value) {}
```

## containsValue(Object value) 是否包含value
```
public boolean containsValue(Object value) {}
```

## containsKey(Object key) 是否包含key
```
public boolean containsKey(Object key) {
        return getEntry(key) != null;
    }
```

## get(Object key) 根据key获取value
```
public synchronized V get(Object key) {}
``` 

## put(K key, V value) 将指定的key value放入Hashtable中,若已存在key,就替换旧值
```
public synchronized V put(K key, V value) {}
``` 

## remove(Object key) 根据key删除
```
public synchronized V remove(Object key) {
	removeEntryForKey(key);
}
```

## putAll(Map<? extends K, ? extends V> m) 把指定的元素 全部放入HashMap中,已经存在的key,会把旧value覆盖掉
```
public synchronized void putAll(Map<? extends K, ? extends V> m) {}
```

## clear() 清空
```
public synchronized void clear(){}
```

## clone() 浅拷贝
```
public Object clone() {}
```

## toString()
```
public synchronized String toString() {}
```



# 参考