---
title: Executor框架简介
date: 2016-08-11 15:16:11
categories: 
- Java基础
- 并发包
tags:
- Executor
---

Executor框架是在Java5中引入的，可以通过该框架来控制线程的启动，执行，关闭，简化并发编程。Executor框架把任务提交和执行解耦，要执行任务的人只需要把任务描述清楚提交即可，任务的执行提交人不需要去关心。

通过Executor框架来启动线程比使用Thread更好，更易管理，效率高，避免this逃逸问题。

Executor的实现还提供了对生命周期的支持，以及统计信息收集，应用程序管理机制和性能监视等机制。

<!-- more -->

# Executor框架

源码版本：
>jdk1.7.0_71

Executor框架由3大部分组成：

1. 任务：被执行任务需要实现接口Runnable、Callable。
2. 任务执行：任务执行机制的核心接口Executor，继承Executor的ExecutorService。CompletionService等。
3. 异步计算的结果：Future和实现了Future接口的FutureTask类。


# Executor
是一个接口，只定义了一个方法`void execute(Runnable command);`该方法接受一个Runnable实例，作用就是执行提交的任务，实现在子类中。

# ExecutorService
也是一个接口，继承自Executor接口，提供了更多的方法，提供了生命周期的管理方法，以及可跟踪一个或多个异步任务执行状况的方法。

ExecutorService的生命周期包括三种状态：运行，关闭，终止。创建后便进入运行状态，当调用了shutdown()方法时，进入关闭状态，此时不再接受新任务，但是它还在执行已经提交了的任务，当所有的任务执行完后，便达到了终止状态。

## shutdown()
关闭当前服务，当调用此方法时，它将不再接受新的任务，已经提交的任务，还要继续执行完毕。

## shutdownNow()

```
List<Runnable> shutdownNow();
```

关闭当前服务，尚未执行的任务，不再执行；正在执行的任务，通过线程中断thread.interrupt。方法返回等待执行的任务列表。

## isShutdown()
程序是否已经关闭。

## isTerminated()
程序是否已经终止。已经关闭并且所有的任务都执行完成，返回true，其他返回false。

## awaitTermination()

```
boolean awaitTermination(long timeout, TimeUnit unit)
        throws InterruptedException;
```
请求关闭、发生超时或者当前线程中断，无论哪一个首先发生之后，都将导致阻塞，直到所有任务完成执行。如果程序终止，返回true，如果超时，返回false，等待时发生中断，抛出异常。

## submit(Callable<T> task)

```
<T> Future<T> submit(Callable<T> task);
```
向Executor提交一个Clallable任务，并返回一个Future；

## submit(Runnable task, T result)

```
<T> Future<T> submit(Runnable task, T result);
```
向Executor提交一个Runnable任务，并返回一个Future；

## submit(Runnable task)

```
Future<?> submit(Runnable task);
```
向Executor提交一个Runnable任务，并返回一个Future；

## invokeAll（）
执行所有任务列表，当所有任务执行完成之后，返回Future列表。

## invokeAny()

在执行的任务集合中，任何一个完成就返回。



# Executors
提供了一系列静态工厂方法用于创建各种线程池。返回的线程池都实现了ExecutorService接口。

如果没有特殊要求，请尽量使用此类中提供的静态方法生成线程池。

大部分方法的底层实现都在ThreadPoolExecutor类中，有关ThreadPoolExecutor介绍请往下翻。ThreadPoolExecutor的构造函数如下：

```
public ThreadPoolExecutor(int corePoolSize,
                          int maximumPoolSize,
                          long keepAliveTime,
                          TimeUnit unit,
                          BlockingQueue<Runnable> workQueue,
                          ThreadFactory threadFactory,
                          RejectedExecutionHandler handler) 
```

## newFixedThreadPool(int nThreads)

创建一个可重用的固定线程数的线程池，以共享无界队列方式来运行这些线程。

```
public static ExecutorService newFixedThreadPool(int nThreads) {
    return new ThreadPoolExecutor(nThreads, nThreads,
                                  0L, TimeUnit.MILLISECONDS,
                                  new LinkedBlockingQueue<Runnable>());
}
```
corePoolSize和maximumPoolSize大小一样，使用无界queue的话maximumPoolSize参数是没有意义的。改方法使用的是LinkedBlockingQueue无界队列。keepAliveTime为0.

## newFixedThreadPool(nThreads, ThreadFactory)

使用给定的ThreadFactory创建一个可重用的固定线程数的线程池，以共享无界队列方式运行这些线程。

## newSingleThreadExecutor
创建一个使用单个线程的线程池，使用无界队列来运行该线程。

## newSingleThreadExecutor(ThreadFactory)

使用给定的ThreadFactory创建一个单个线程的线程池，使用无界队列来运行该线程。

## newCachedThreadPool
创建一个可根据需要创建新线程的线程池，以前构造的线程可用时将重用它们。对于执行很多短期异步任务的程序而言，这些线程池通常可提高程序性能。

```
public static ExecutorService newCachedThreadPool() {
    return new ThreadPoolExecutor(0, Integer.MAX_VALUE,
                                  60L, TimeUnit.SECONDS,
                                  new SynchronousQueue<Runnable>());
}
```
maximumPoolSize为最大，是一个无界线程池。workqueue使用SynchronousQueue，该queue中每个插入操作必须等待另一个线程的对应移除操作。它的主要提供的功能是线程复用，但不能控制线程数量。

## newCachedThreadPool(ThreadFactory)
使用给定的ThreadFactory创建一个可根据需要创建新线程的线程池，以前构造的线程可用时将重用它们。对于执行很多短期异步任务的程序而言，这些线程池通常可提高程序性能。

## newSingleThreadScheduledExecutor
创建一个使用单个线程的线程池，该线程池支持定时以及周期性执行任务的功能。

## newScheduledThreadPool(corePoolSize)
创建一个指定数量的线程的线程池，该线程池支持定时以及周期性执行任务的功能。



# AbstractExecutorService
ExecutorService方法的默认实现类。

# ScheduledExecutorService

一个可定时调度任务的接口。扩展了ExecutorService。支持Future和定期执行任务。

# ScheduledThreadPoolExecutor
ScheduledExecutorService的实现，一个可定时调度任务的线程池。

# ThreadPoolExecutor
线程池，可以通过调用Executors的静态工厂方法来创建线程池并返回一个ExecutorService对象。

ThreadPoolExecutor是Executors的底层实现，看Executors源码可知，大部分的生成线程池的方法内部都是调用此类的方法。

该类继承了AbstractExecutorService。

## 构造方法

```
public ThreadPoolExecutor(int corePoolSize,
                          int maximumPoolSize,
                          long keepAliveTime,
                          TimeUnit unit,
                          BlockingQueue<Runnable> workQueue,
                          ThreadFactory threadFactory,
                          RejectedExecutionHandler handler) {
    if (corePoolSize < 0 ||
        maximumPoolSize <= 0 ||
        maximumPoolSize < corePoolSize ||
        keepAliveTime < 0)
        throw new IllegalArgumentException();
    if (workQueue == null || threadFactory == null || handler == null)
        throw new NullPointerException();
    this.corePoolSize = corePoolSize;
    this.maximumPoolSize = maximumPoolSize;
    this.workQueue = workQueue;
    this.keepAliveTime = unit.toNanos(keepAliveTime);
    this.threadFactory = threadFactory;
    this.handler = handler;
}
```

* `corePoolSize` 池中所保存的核心线程数，包括空闲线程。
* `maximumPoolSize`池中允许创建的最大线程数。当workqueue使用无界队列LinkedBlockingQueue等时，此参数无效。
* `keepAliveTime`当前线程池线程总数大于核心线程数时，终止多余的空闲线程时间。
* `unit`keepAliveTime参数的时间单位
* `workQueue`工作队列，如果当前线程池达到核心线程数时，且当前所有线程都处于活动状态，则将新加入的任务放到此队列中。仅保持由execute方法提交的Runnable任务。
	
- ArrayBlockingQueue 基于数组结构有界队列，FIFO原则对任务进行排序，队列满了之后的任务，调用拒绝策略。
- LinkedBlockingQueue 基于链表结构的无界队列，FIFO原则对任务进行排序。
- SynchronousQueue 直接将任务提交给线程而不是将它加入到队列，实际上此队列是空的。每个插入的操作必须等到另一个调用移除的操作；如果新任务来了线程池没有任何可用线程处理的话，则调用拒绝策略。
- PriorityBlockingQueue 具有优先级的队列的有界队列，可以自定义优先级；默认是按自然排序。
	
* `threadFactory` 执行程序创建新线程时使用的工厂。
* `handler` 超出线程范围和队列容量时，执行被阻塞，此时可以选择用此handler进行处理。


# Callable，Future与FutureTask

在之前创建线程的时候都是继承Thread或者实现Runnable接口，这种方法在任务完成后无法获取执行结果。而在Java5之后有了Runnable和Future，可以在任务执行完之后得到任务执行结果。

## Callable

Callable能产生结果，Future能拿到结果。Callable和Runnable接口类似，Runnable不会返回结果，也无法抛出返回结果的异常，而Callable可以返回值，这个值可被Future拿到。

Callable一般和ExecutorService一起使用，ExecutorService的submit方法提交的是Runnable或者是Callable类型的Task。

# Future
Future对具体提交的Callable任务执行结果进行取消，查询是否完成，获取结果。可以通过get方法获取执行结果，该方法会阻塞直到任务返回结果。Future接口的具体实现都在FutureTask中。

### cancel()方法

```
boolean cancel(boolean mayInterruptIfRunning);
```
可以取消任务，成功返回true，失败返回false。

参数mayInterruptIfRunning表示是否取消正在执行但是没有执行完成的任务，true可以取消，false不取消

* 如果任务已经完成，无论参数是true或false，都返回false
* 如果任务正在执行，若参数为true，返回true；若参数为false，返回false；
* 如果任务还未执行，无论参数是true或false，都返回true。

### isCancelled()方法
表示任务是否被取消成功。

### isDone()方法
表示任务是否已经完成。

### get()方法

```
V get() throws InterruptedException, ExecutionException;
```

用来获取执行结果，会一直阻塞等到任务执行完成之后返回。

### get(timeout,unit)方法

```
V get(long timeout, TimeUnit unit)
        throws InterruptedException, ExecutionException, TimeoutException;
```
用来获取执行结果，指定时间内获取不到结果，返回null


## FutureTask
FutureTask实现了RunnableFuture接口，RunnableFuture接口继承了Runnable和Future接口。

是Future接口的唯一实现。

FutureTask是为了弥补Thread的不足而设计的，它可以让程序员准确地知道线程什么时候执行完成并获得到线程执行完成后返回的结果（如果有需要）

可用于要异步获取执行结果或取消执行任务的场景。

FutureTask是一种可以取消的异步的计算任务。它的计算是通过Callable实现的，它等价于可以携带结果的Runnable，并且有三个状态：等待、运行和完成。完成包括所有计算以任意的方式结束，包括正常结束、取消和异常。

利用开始和取消计算的方法、查询计算是否完成的方法和获取计算结果的方法，此类提供了对 Future 的基本实现。仅在计算完成时才能获取结果；如果计算尚未完成，则阻塞 get 方法。一旦计算完成，就不能再重新开始或取消计算。

# CompletionService
我们可以通过线程池的submit方法提交一个Callable任务，利用返回的Future的get方法来获取任务运行的结果，但是这种方法需要自己循环获取task，而且get方法会阻塞。

还可以用CompletionService来实现，CompletionService维护一个保存Future对象的BlockQueue，当Future对象状态是结束的时候，会加入到队列中，可以通过take方法，取出Future对象。

* submit()方法，用于提交任务。
* take()方法，获取任务结果。获取并移除表示下一个已完成任务的Future，如果任务不存在，则等待。
* poll()方法，获取任务结果。获取并移除表示下一个已完成任务的Future，如果不存在，则返回null。

方法的实现都在ExecutorCompletionService中。

# ExecutorCompletionService

ExecutorCompletionService是CompletionService的实现类，在submit任务时，会创建一个QueueingFuture，然后将创建的QueueingFuture交给executor去完成任务的执行。

QueueingFuture继承自FutureTask类。

内部维护了一个可阻塞的队列，具体实现使用LinkedBlockingQueue。

提交到ExecutorCompletionService的任务会在内部被包装成QueueingFuture，并由内部的executor来执行这个任务，当任务执行完成后，会被加入到内部的队列里面，外部程序就可以通过take或者poll方法来获取完成的任务了。


# 实例
摘自[线程池实例：使用Executors和ThreadPoolExecutor](http://www.importnew.com/8542.html)这篇文章，如有需要点击标题查看全文。

首先创建一个实现Runable接口的类：

```
package com.journaldev.threadpool;
 
public class WorkerThread implements Runnable {
 
    private String command;
 
    public WorkerThread(String s){
        this.command=s;
    }
 
    @Override
    public void run() {
        System.out.println(Thread.currentThread().getName()+" Start. Command = "+command);
        processCommand();
        System.out.println(Thread.currentThread().getName()+" End.");
    }
 
    private void processCommand() {
        try {
            Thread.sleep(5000);
        } catch (InterruptedException e) {
            e.printStackTrace();
        }
    }
 
    @Override
    public String toString(){
        return this.command;
    }
}
```

测试程序：

```
package com.journaldev.threadpool;
 
import java.util.concurrent.ExecutorService;
import java.util.concurrent.Executors;
 
public class SimpleThreadPool {
 
    public static void main(String[] args) {
        ExecutorService executor = Executors.newFixedThreadPool(5);
        for (int i = 0; i < 10; i++) {
            Runnable worker = new WorkerThread("" + i);
            executor.execute(worker);
          }
        executor.shutdown();
        while (!executor.isTerminated()) {
        }
        System.out.println("Finished all threads");
    }
 
}

```

上面的代码中，创建了一个能包含5个线程的固定大小的线程池，for循环向线程池提交10个任务。首先启动5个线程，其他的任务等待，当其中的任务执行完成，等待的任务会被选中一个执行。


# 参考

[http://my.oschina.net/smartsales/blog/529044](http://my.oschina.net/smartsales/blog/529044)

[http://blog.csdn.net/ns_code/article/details/17465497](http://blog.csdn.net/ns_code/article/details/17465497)

[http://www.cnblogs.com/MOBIN/p/5436482.html](http://www.cnblogs.com/MOBIN/p/5436482.html)

[http://www.infoq.com/cn/articles/executor-framework-thread-pool-task-execution-part-01](http://www.infoq.com/cn/articles/executor-framework-thread-pool-task-execution-part-01)

[http://my.oschina.net/pingpangkuangmo/blog/666762](http://my.oschina.net/pingpangkuangmo/blog/666762)

[http://blog.csdn.net/gemmem/article/details/8956703](http://blog.csdn.net/gemmem/article/details/8956703)

[http://uule.iteye.com/blog/1539084](http://uule.iteye.com/blog/1539084)

[http://brokendreams.iteye.com/blog/2252800](http://brokendreams.iteye.com/blog/2252800)