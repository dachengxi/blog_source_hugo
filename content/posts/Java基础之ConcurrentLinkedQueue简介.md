---
title: Java基础之ConcurrentLinkedQueue简介
date: 2016-08-05 22:06:52
categories: 
- Java基础
---

ConcurrentLinkedQueue是一个基于链表的、无界的、线程安全的、非阻塞队列，适合高并发的场景。

<!--more-->

ConcurrentLinkedQueue基于Michael & Scott非阻塞队列算法进行实现，做了一些修改。

非阻塞的性能较好，采用CAS，没有使用锁，但是又尽量减少CAS的次数提升性能。次出队和入队的时候并不是每次都使用CAS更新head和tail的指向，这样就可以减少CAS次数，但是也意味着head和tail并不总是指向真的头尾元素。

# head结点和tail结点

ConcurrentLinkedQueue有head结点和tail结点，每次元素入队列的时候，tail并不总是指向最后一个结点；出队列的时候head也并不总是指向第一个结点。

假设tail现在指向最后一个结点，此时tail.next==null，有新元素入队列的时候，会设置tail.next=新结点，tail并不会指向新的最后一个结点，而是等下一个元素入队列的时候修改tail的指向。这样就不用每次都需要cas修改tail结点，而是每两次才需要cas修改tail结点，减少cas次数，提高效率。

假设head指向第一个结点，此时head.item不为null，此时直接将第一个结点中的元素返回，并设置第一个结点的item=null，下一次再有需要出队列一个元素的时候，此时head.item==null，需要将head后面的这个结点元素出队列，并将head指向刚才head的后面的后面的结点。head也是通过减少cas的次数来提升效率。

# 关于结点的说明

由于只使用了CAS，未使用锁，为了保证可见性，结点中的字段都是用了volatile类型。但也不是每个操作都需要使用volatile来保证可见性，因为volatile使用涉及到了内存屏障。

在新建结点的时候使用了`UNSAFE.putObject`方法设置结点的item，这里就是将一个volatile类型的变量以普通变量方式进行更新，不涉及到内存屏障，用来提升性能。因为新建结点设置元素的时候，并不会涉及到多线程竞争，无需保证可见性。

另外节点中还有一个lazySetNext方法，代码如下：

```
void lazySetNext(Node<E> val) {
	UNSAFE.putOrderedObject(this, nextOffset, val);
}
```

使用了`UNSAFE.putOrderedObject`方法，此方法设置完对应值后，不保证可见性，通常用在volatile类型的字段上。

在对volatile字段更新时，为了保证可见性，会使用到StoreLoad内存屏障，也就是最重的那个内存屏障，而使用UNSAFE.putOrderedObject则只使用到StoreStore内存屏障，比StoreLoad内存屏障性能更好。但是需要牺牲可见性，更新后可能不能马上被别的线程可见。


# 源码分析

参照源码解析：[https://github.com/dachengxi/Java8u192SourceCode](https://github.com/dachengxi/Java8u192SourceCode)

# 参考

[http://www.infoq.com/cn/articles/ConcurrentLinkedQueue](http://www.infoq.com/cn/articles/ConcurrentLinkedQueue)

[http://www.cnblogs.com/skywang12345/p/3498995.html](http://www.cnblogs.com/skywang12345/p/3498995.html)

[http://vickyqi.com/2015/10/29/JDK%E5%B9%B6%E5%8F%91%E5%B7%A5%E5%85%B7%E7%B1%BB%E6%BA%90%E7%A0%81%E5%AD%A6%E4%B9%A0%E7%B3%BB%E5%88%97%E2%80%94%E2%80%94ConcurrentLinkedQueue/](http://vickyqi.com/2015/10/29/JDK%E5%B9%B6%E5%8F%91%E5%B7%A5%E5%85%B7%E7%B1%BB%E6%BA%90%E7%A0%81%E5%AD%A6%E4%B9%A0%E7%B3%BB%E5%88%97%E2%80%94%E2%80%94ConcurrentLinkedQueue/)