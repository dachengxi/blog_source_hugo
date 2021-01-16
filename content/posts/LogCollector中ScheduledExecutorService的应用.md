---
title: LogCollector中ScheduledExecutorService的应用
date: 2010-05-21 22:39:34
categories: 
- LogCollector
tags:
- LogCollector
---

LogCollector中ScheduledExecutorService的使用学习。

<!--more-->

在定时发送心跳包的场景下可以使用，比如scheduleWithFixedDelay，每隔一定时间就发送一次心跳。

使用方法示例：

```java
public class ScheduleWithFixedDelayExample {

    private static ScheduledExecutorService scheduledExecutorService = Executors.newScheduledThreadPool(
        1,
        new NoneDaemonThreadFactory("testFixedDelay")
    );

    public static void main(String[] args) {
        scheduledExecutorService.scheduleWithFixedDelay(new TestTask1(), 0L, 3, TimeUnit.SECONDS);
    }
}
```

```java
public class TestTask1 implements Runnable {

    @Override
    public void run() {
        System.out.println("任务开始执行时间：" + new SimpleDateFormat("yyyy-MM-dd HH:mm:ss").format(new Date()));
        long start = System.currentTimeMillis();
        int random = new Random().nextInt(10);
        try {
            Thread.sleep(random * 1000);
        } catch (InterruptedException e) {
            e.printStackTrace();
        }
        System.out.println("任务执行花费时间：" + (System.currentTimeMillis() - start));
        System.out.println("任务结束执行时间：" + new SimpleDateFormat("yyyy-MM-dd HH:mm:ss").format(new Date()));
    }
}
```

```java
public class NoneDaemonThreadFactory implements ThreadFactory {

    private final static AtomicInteger poolNumber = new AtomicInteger(1);

    private final AtomicInteger threadNumber = new AtomicInteger(1);

    private final ThreadGroup group;

    private final String threadNamePrefix;

    public NoneDaemonThreadFactory(String prefix) {
        SecurityManager securityManager = System.getSecurityManager();

        group = securityManager == null ? Thread.currentThread().getThreadGroup() : securityManager.getThreadGroup();

        threadNamePrefix = String.format("%s-%d-", prefix, poolNumber.getAndIncrement());
    }

    @Override
    public Thread newThread(Runnable r) {
        Thread thread = new Thread(
            group,
            r,
            threadNamePrefix + threadNumber.getAndIncrement(),
            0
        );

        if (thread.getPriority() != Thread.NORM_PRIORITY) {
            thread.setPriority(Thread.NORM_PRIORITY);
        }
        return thread;
    }
}
```

- scheduleWithFixedDelay，在一次任务结束后，间隔指定的时间，再继续执行下一次任务，不管一次任务执行多长时间，在这次任务结束后都会暂停指定的时间，接下来再执行下面的任务。就是说我不管，我每次任务完成都必须要休息一定时间。
- scheduleAtFixedRate，每个任务都在指定的时间间隔内执行，如果一个任务瞬间执行完，指定的时间间隔还有很多剩余的，下一个任务也不会执行；如果一个任务在指定的时间间隔没有执行完，占用了下个任务的时间，那这个任务执行完后下个任务立马就开始。

