---
title: Java中的DelayQueue延时队列
date: 2016-05-29 22:15:25
categories: 
- Java
tags:
- Java
---

DelayQueue使用和原理学习。

<!-- more -->

# 简介

DelayQueue是一个无界阻塞队列，支持延时获取元素。内部使用PriorityQueue优先级队列来存储元素，存储的元素需要实现Delayed接口。

也就是在往DelayQueue中放元素的时候，该元素必须实现Delayed接口，并且可以指定一个需要延迟的时间，等过了一段时间到了我们指定的延迟时间后，可以获取到该元素，继续针对该元素处理。

# 示例

模拟延迟发送短信功能，发短信的时候可以指定延时多久进行发送。

```java
/**
 * 延迟短信
 */
public class DelayedSms implements Delayed {

    /**
     * 短信内容
     */
    private String sms;

    /**
     * 短信创建时间
     */
    private long createTime;

    /**
     * 短信发送时间
     */
    private long sendTime;

    /**
     * 返回剩余的时间
     * @param unit
     * @return
     */
    @Override
    public long getDelay(TimeUnit unit) {
        long remaining = unit.convert(sendTime - System.currentTimeMillis(), TimeUnit.MILLISECONDS);
        System.out.println("剩余时间：" + remaining + "ms");
        return remaining;
    }

    /**
     * 延时队列内部比较排序
     * @param other
     * @return
     */
    @Override
    public int compareTo(Delayed other) {
        return Long.compare(this.getDelay(TimeUnit.MILLISECONDS), other.getDelay(TimeUnit.MILLISECONDS));
    }

    // ... getter and setter...
    @Override
    public String toString() {...}
}
```

```java
public class SmsSender {

    private DelayQueue<DelayedSms> queue = new DelayQueue<>();

    public SmsSender() {
        new Thread(new SmsSendTask()).start();
    }

    /**
     * 发送延时短信
     * @param sms 短信内容
     * @param createTime 短信创建时间
     * @param delay 要延长的时间，毫秒
     */
    public void sendDelaySms(String sms, long createTime, long delay) {
        DelayedSms delayedSms = new DelayedSms();
        delayedSms.setSms(sms);
        delayedSms.setCreateTime(createTime);
        delayedSms.setSendTime(createTime + delay);

        queue.offer(delayedSms);
    }

    private class SmsSendTask implements Runnable {

        @Override
        public void run() {
            while (true) {
                try {
                    DelayedSms sms = queue.take();
                    sendSms(sms);
                } catch (InterruptedException e) {
                    e.printStackTrace();
                }
            }
        }
    }

    private void sendSms(DelayedSms sms) {
        System.out.println("发送短信：" + sms.getSms() + "，createTime：" + new Date(sms.getCreateTime()) + "，sendTime: " + new Date(sms.getSendTime()) + "，发送时间：" + new Date(System.currentTimeMillis()));
    }
}
```

```java
public class Client {

    public static void main(String[] args) throws InterruptedException {
        SmsSender smsSender = new SmsSender();

        long createTime = System.currentTimeMillis();
        // 延时20s发送
        smsSender.sendDelaySms("短信1", createTime, 20 * 1000);

        // 延时30秒发送
        smsSender.sendDelaySms("短信2", createTime, 30 * 1000);

        // 延时10秒发送
        smsSender.sendDelaySms("短信3", createTime, 10 * 1000);

    }
}
```

# 解析

延时队列重点：阻塞队列、PriorityQueue、Delayed。延时队列是使用优先级队列来实现的一个阻塞队列，队列中存放的是实现接口Delayed的元素，优先队列比较是根据指定的延时时间。

## 阻塞队列

延时队列是阻塞队列，获取元素的时候如果没有元素到期，获取元素的线程会被阻塞。

## Delayed

延时队列中存放的元素必须实现Delayed接口，该接口定义了一个getDelay方法，是我们必须要实现的方法，该方法用来返回元素到过期时间还剩余多少时间。

Delayed还继承了接口Comparable，compareTo方法也是我们必须要实现的，该方法用来确定元素在PriorityQueue中的顺序。

## PriorityQueue

延时队列内部使用PriorityQueue实现，PriorityQueue中存放的元素必须实现Delayed接口，根据元素的compareTo方法来确定元素顺序

# 源码

```java
public class DelayQueue<E extends Delayed> extends AbstractQueue<E>
    implements BlockingQueue<E> {

    /**
     * 重入锁，保证队列操作的安全
     */
    private final transient ReentrantLock lock = new ReentrantLock();

    /**
     * 优先级队列，用来存储元素，可以根据我们在compareTo方法中指定的算法来排序我们的元素
     */
    private final PriorityQueue<E> q = new PriorityQueue<E>();

    /**
     * leader指向第一个从队列获取元素时阻塞等待的线程，用来优化内部阻塞通知，
     * 减少其他线程不必要的等待时间
     *
     * 采用的是Leader-Follower模式
     */
    private Thread leader = null;

    /**
     * 队列头部有可用新元素或者新线程需要成为新的leader时需要被通知
     */
    private final Condition available = lock.newCondition();

   
    public boolean offer(E e) {
        // 添加元素进队列，先加锁
        final ReentrantLock lock = this.lock;
        lock.lock();
        try {
            // 使用优先级队列，添加元素进队列，优先级队列会对元素按照指定规则排序
            q.offer(e);
            // 添加完元素后，获取优先级队列的队头元素
            // 如果刚添加的元素在队列头部，说明刚添加的元素就是要到期的元素
            if (q.peek() == e) {
                // leader置为null
                leader = null;
                // 唤醒阻塞在等待队列的线程
                available.signal();
            }
            return true;
        } finally {
            lock.unlock();
        }
    }

    public E take() throws InterruptedException {
        // 出队列前先加锁
        final ReentrantLock lock = this.lock;
        lock.lockInterruptibly();
        try {
            for (;;) {
                // 获取队列头的元素
                E first = q.peek();
                //队列中没有元素，
                if (first == null)
                    // 当前线程等待
                    available.await();
                else {
                    // 获取到了队列头元素
                    // 查看下我们定义元素时设置的延时时间规则，看是否到期
                    long delay = first.getDelay(NANOSECONDS);
                    // 队列头元素已经过期，直接返回队列头元素
                    if (delay <= 0)
                        return q.poll();
                    first = null; // don't retain ref while waiting
                    // 走到这里说明队列中有元素，但都还没到过期时间
                    // 如果leader存在，说明有其他的线程已经调用了take获取
                    if (leader != null)
                        // 当前线程挂起等待
                        available.await();
                    else {
                        // leader为空，将当前线程变成leader
                        Thread thisThread = Thread.currentThread();
                        leader = thisThread;
                        try {
                            // 上面已经有了剩余的时间，当前线程就可以直接挂起等待这剩余的一段时间
                            available.awaitNanos(delay);
                        } finally {
                            if (leader == thisThread)
                                leader = null;
                        }
                    }
                }
            }
        } finally {
            // leader处理完，返回了需要的元素，这里要唤醒其他的follower
            if (leader == null && q.peek() != null)
                available.signal();
            lock.unlock();
        }
    }
}

```



