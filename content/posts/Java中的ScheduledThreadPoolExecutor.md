---
title: Java中的ScheduledThreadPoolExecutor
date: 2016-05-30 17:57:06
categories: 
- Java
tags:
- Java
---

ScheduledThreadPoolExecutor使用和原理学习。

<!-- more -->

# 原理

ScheduledThreadPoolExecutor提供了延迟和周期执行功能，内部实现相当于使用DelayQueue延时队列、PriorityQueue优先级队列和Delayed三者来配合实现的功能，在使用延时队列实现我们自己的业务功能时，也会使用这三个组件来配合。但是实际上ScheduledThreadPoolExecutor不是直接使用这三个组件来实现，而是在内部实现了几个功能类似的内部类：

- DelayedWorkQueue，功能相当于DelayQueue，只不过这个内部没有依赖PriorityQueue，而是直接实现了堆排序功能。
- ScheduledFutureTask，相当于实现了Delayed接口的业务类，ScheduledFutureTask的父接口的父接口ScheduledFuture继承了Delayed接口，ScheduledFutureTask实现了getDelay和compareTo方法，这两个方法可以参考DelayQueue解析中的使用。



# 方法

- schedule，给定延迟后，执行任务
- scheduleAtFixedRate，每个任务都在指定的时间间隔内执行，如果一个任务瞬间执行完，指定的时间间隔还有很多剩余的，下一个任务也不会执行；如果一个任务在指定的时间间隔没有执行完，占用了下个任务的时间，那这个任务执行完后下个任务立马就开始。
- scheduleWithFixedDelay，在一次任务结束后，间隔指定的时间，再继续执行下一次任务，不管一次任务执行多长时间，在这次任务结束后都会暂停指定的时间，接下来再执行下面的任务。就是说我不管，我每次任务完成都必须要休息一定时间。

# 源码

```java
/**
     * 给定延迟后，执行任务，只会执行一次
     */
    public ScheduledFuture<?> schedule(Runnable command,
                                       long delay,
                                       TimeUnit unit) {
        if (command == null || unit == null)
            throw new NullPointerException();
        // 创建执行的Task实例，由于schedule只执行一次，所以实例化ScheduledFutureTask的时候周期数是0
        RunnableScheduledFuture<?> t = decorateTask(command,
            new ScheduledFutureTask<Void>(command, null,
                                          triggerTime(delay, unit)));
        delayedExecute(t);
        return t;
    }
```

```java
/**
     * 每个任务都在指定的时间间隔内执行，如果一个任务瞬间执行完，
     * 指定的时间间隔还有很多剩余的，下一个任务也不会执行；如果一
     * 个任务在指定的时间间隔没有执行完，占用了下个任务的时间，
     * 那这个任务执行完后下个任务立马就开始。
     */
    public ScheduledFuture<?> scheduleAtFixedRate(Runnable command,
                                                  long initialDelay,
                                                  long period,
                                                  TimeUnit unit) {
        if (command == null || unit == null)
            throw new NullPointerException();
        if (period <= 0)
            throw new IllegalArgumentException();
        // 实例化ScheduledFutureTask的时候周期数为指定的周期数
        ScheduledFutureTask<Void> sft =
            new ScheduledFutureTask<Void>(command,
                                          null,
                                          triggerTime(initialDelay, unit),
                                          unit.toNanos(period));
        RunnableScheduledFuture<Void> t = decorateTask(command, sft);
        sft.outerTask = t;
        // 延迟执行
        delayedExecute(t);
        return t;
    }
```

```java
/**
     * 在一次任务结束后，间隔指定的时间，再继续执行下一次任务，
     * 不管一次任务执行多长时间，在这次任务结束后都会暂停指定的
     * 时间，接下来再执行下面的任务。就是说我不管，我每次任务完成
     * 都必须要休息一定时间。
     */
    public ScheduledFuture<?> scheduleWithFixedDelay(Runnable command,
                                                     long initialDelay,
                                                     long delay,
                                                     TimeUnit unit) {
        if (command == null || unit == null)
            throw new NullPointerException();
        if (delay <= 0)
            throw new IllegalArgumentException();
        // 实例化ScheduledFutureTask的时候周期数是指定的延时时长，并且是负数
        ScheduledFutureTask<Void> sft =
            new ScheduledFutureTask<Void>(command,
                                          null,
                                          triggerTime(initialDelay, unit),
                                          unit.toNanos(-delay));
        RunnableScheduledFuture<Void> t = decorateTask(command, sft);
        sft.outerTask = t;
        // 延迟执行
        delayedExecute(t);
        return t;
    }
```

ScheduledFutureTask是实现了Delayed接口的类，重写的getDelay和compareTo方法跟我们自己实现业务逻辑一样。

run方法：

```java
public void run() {
            // 是否是周期任务
            boolean periodic = isPeriodic();
            // 判断当前线程状态是否能执行任务
            if (!canRunInCurrentRunState(periodic))
                cancel(false);
            // 不是周期性任务，直接执行
            else if (!periodic)
                ScheduledFutureTask.super.run();
            // 周期性任务，执行任务，设置下次执行时间
            else if (ScheduledFutureTask.super.runAndReset()) {
                // 设置下次执行时间
                // scheduleAtFixedRate和scheduleWithFixedDelay的period不同，处理也不一样
                setNextRunTime();
                // 将下次要执行的任务添加到队列中
                reExecutePeriodic(outerTask);
            }
        }
```

```java
private void setNextRunTime() {
            // scheduleAtFixedRate的period是大于0的
            // scheduleWithFixedDelay是小于0的
            long p = period;
            //scheduleAtFixedRate，下次时间执行的时间等于上次开始执行时间加上周期时间
            if (p > 0)
                time += p;
            else
                // 下次执行时间等于当前时间加上周期时间，也就是要暂停period时间
                time = triggerTime(-p);
        }
```

