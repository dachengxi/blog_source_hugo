---
title: ConcurrentLinkedQueue简介
date: 2016-08-05 22:06:52
categories: 
- Java基础
- 并发集合
tags:
- ConcurrentLinkedQueue
---

ConcurrentLinkedQueue是一个基于链表的无界线程安全队列，非阻塞实现方式，先进先出，适合高并发的场景。

非阻塞的性能较好，采用CAS，避免加锁的时间，保证数据一致性。

采用“wait-free”算法实现。

（此部分源码看的比较吃力，很多不懂的地方，还有很多不知道的地方，希望不要误导读者，有好的文章之类的，希望能推荐下，谢谢）

<!-- more -->

# 定义
ConcurrentLinkedQueue继承AbstractQueue<E>，实现 Queue<E>, Serializable接口。内部通过链表实现。

ConcurrentLinkedQueue链表节点Node的数据item是volatile类型的，next节点也是volatile类型的。

默认构造一个空的ConcurrentLinkedQueue，head=tail= new Node<E>(null)

# 源码分析
>jdk1.7.0_71

## add(E)方法
添加元素到队列尾部，实现是用offer，下面会说offer方法。

```
public boolean add(E e) {
    return offer(e);
}
```
## offer(E)方法

添加元素到队列尾部。

```
public boolean offer(E e) {
    checkNotNull(e);
    //创建新节点
    final Node<E> newNode = new Node<E>(e);
	
	//循环开始，tail赋值给t，t赋值给p，此处是死循环，保证该入队操作能够一直重试，直到入队成功
    for (Node<E> t = tail, p = t;;) {
    	//p的next赋值给q
        Node<E> q = p.next;
        //如果q是null，说明tail的next是null，tail指向的是队列尾部，所以tail的next应该始终是null，此处表示p是最后一个节点
        if (q == null) {
            // p是尾节点，则p的next节点是要入队列的节点
            //cas操作，若失败表示其他线程对尾节点进行了修改，重新循环
            //将p（p指向tail）的next指向新节点，若成功，进入if语句
            if (p.casNext(null, newNode)) {
                //cas操作成功后，判断p是否等于t，p不等于t，设置newNode为新的尾节点,p不等于t表示什么？(此处有不明白，希望高手帮帮忙。)
                if (p != t) // hop two nodes at a time
                    casTail(t, newNode);  // Failure is OK.
                return true;
            }
            // p和q相等（这部分不是太理解，希望高手帮忙下）
        }
        else if (p == q)
            // We have fallen off list.  If tail is unchanged, it
            // will also be off-list, in which case we need to
            // jump to head, from which all live nodes are always
            // reachable.  Else the new tail is a better bet.
            p = (t != (t = tail)) ? t : head;
        else
            // Check for tail updates after two hops.
            p = (p != t && t != (t = tail)) ? t : q;
    }
}
```
## poll()方法

删除并返回头部元素

```
public E poll() {
	//设置循环标记
    restartFromHead:
    for (;;) {
        for (Node<E> h = head, p = h, q;;) {
            E item = p.item;
			//表头数据不为null，cas操作设置表头数据为null成功进入if
            if (item != null && p.casItem(item, null)) {
            //p不等于h，队列头发生了变化，更新队列头为p，返回原来队列头的item  
                if (p != h) // hop two nodes at a time
                    updateHead(h, ((q = p.next) != null) ? q : p);
                return item;
            }
            //队列头下一个节点为null，更新头尾p返回null
            else if ((q = p.next) == null) {
                updateHead(h, p);
                return null;
            }
            else if (p == q)
                continue restartFromHead;
            else
                p = q;
        }
    }
}
```

## peek()方法
返回队列头部的元素，跟poll方法类似

```
public E peek() {
    restartFromHead:
    for (;;) {
        for (Node<E> h = head, p = h, q;;) {
            E item = p.item;
            if (item != null || (q = p.next) == null) {
                updateHead(h, p);
                return item;
            }
            else if (p == q)
                continue restartFromHead;
            else
                p = q;
        }
    }
}
```

## size()方法
需要遍历队列，不推荐使用，尽量使用isEmpty()

```
public int size() {
    int count = 0;
    for (Node<E> p = first(); p != null; p = succ(p))
        if (p.item != null)
            // Collection.size() spec says to max out
            if (++count == Integer.MAX_VALUE)
                break;
    return count;
}
```

# 参考

[http://www.infoq.com/cn/articles/ConcurrentLinkedQueue](http://www.infoq.com/cn/articles/ConcurrentLinkedQueue)

[http://www.cnblogs.com/skywang12345/p/3498995.html](http://www.cnblogs.com/skywang12345/p/3498995.html)

[http://vickyqi.com/2015/10/29/JDK%E5%B9%B6%E5%8F%91%E5%B7%A5%E5%85%B7%E7%B1%BB%E6%BA%90%E7%A0%81%E5%AD%A6%E4%B9%A0%E7%B3%BB%E5%88%97%E2%80%94%E2%80%94ConcurrentLinkedQueue/](http://vickyqi.com/2015/10/29/JDK%E5%B9%B6%E5%8F%91%E5%B7%A5%E5%85%B7%E7%B1%BB%E6%BA%90%E7%A0%81%E5%AD%A6%E4%B9%A0%E7%B3%BB%E5%88%97%E2%80%94%E2%80%94ConcurrentLinkedQueue/)