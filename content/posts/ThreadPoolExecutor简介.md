---
title: ThreadPoolExecutor简介
date: 2016-05-30 14:04:19
categories: 
- Java基础
- 并发包
tags:
- ThreadPoolExecutor
---

# ThreadPoolExecutor简介
1. 并发包中提供的一个线程池服务

<!-- more -->

## 线程池的工作过程
1. 线程池刚创建,里面没有线程.任务队列是作为参数传进来的.线程池不会立即执行任务.
2. 调用execute()方法添加一个任务,线程池会做如下判断:
	* 如果正在运行的线程数量小于corePoolSize,马上创建线程运行这个任务
	* 如果正在运行的线程数量大于或等于corePoolSize,这个任务放入队列
	* 如果队列满了,且正在运行的线程数量小于maximumPoolSize,就要创建线程运行这个任务
	* 如果队列满了,而且正在运行的线程数量大于或等于maximumPoolSize,通过 handler所指定的策略来处理此任务
3. 当一个线程完成任务时,会从队列取下一个任务来执行
4. 当一个线程空闲时,超过keepAliveTime,线程池会判断,如果当前运行的线程数大于corePoolSize,这个线程会被停掉.

## 任务队列选择
1. ArrayBlockingQueue
2. LinkedBlockingQueue 没有大小限制

## handler选择
1. ThreadPoolExecutor.AbortPolicy() 抛出java.util.concurrent.RejectedExecutionException异常
2. ThreadPoolExecutor.CallerRunsPolicy() 重试添加当前的任务，他会自动重复调用execute()方法
3. ThreadPoolExecutor.DiscardOldestPolicy() 抛弃旧的任务
4. ThreadPoolExecutor.DiscardPolicy() 抛弃当前的任务

# 源码分析
>jdk1.7.0_71

## 构造
```
public ThreadPoolExecutor(
int corePoolSize,//线程池维护线程的最少数量
int maximumPoolSize,//线程池维护线程的最大数量 
long keepAliveTime,//线程池维护线程所允许的空闲时间
TimeUnit unit,//线程池维护线程所允许的空闲时间的单位 
BlockingQueue<Runnable> workQueue,//线程池所使用的缓冲队列
ThreadFactory threadFactory,//执行程序创建新线程时使用的工厂
RejectedExecutionHandler handler//线程池对拒绝任务的处理策略 
){}
```

## execute(Runnable command) 添加任务到线程池
```
public void execute(Runnable command) {
//command为空抛异常
//如果正在运行的线程数量小于corePoolSize,会立即addWorker(),创建新线程运行
//如果正在执行的数量大于等于corePoolSize,将任务放到阻塞队列.如果阻塞队列没有满并且是运行着的,直接放入阻塞队列.放入队列之后还要再做一次检查,如果线程池不在运行状态,把刚才的任务移除,调用reject方法,否则查看worker数量,若为0起一个新的worker去执行任务
//加入队列失败的话,会addWorker尝试一个新的worker去执行任务,新worker创建失败,调用reject方法
}
```

## addWorker(Runnable firstTask, boolean core)
>[详细查看此文章](http://fangjian0423.github.io/2016/03/22/java-threadpool-analysis/)

```
firstTask表示需要跑的任务。
boolean类型的core参数为true的话表示使用线程池的基本大小
为false使用线程池最大大小
```

```
private boolean addWorker(Runnable firstTask, boolean core) {
    retry:
    for (;;) {
        int c = ctl.get();
        int rs = runStateOf(c); // 线程池当前状态

        // 这个判断转换成 rs >= SHUTDOWN && (rs != SHUTDOWN || firstTask != null || workQueue.isEmpty)。 
        // 概括为3个条件：
        // 1. 线程池不在RUNNING状态并且状态是STOP、TIDYING或TERMINATED中的任意一种状态
        // 2. 线程池不在RUNNING状态，线程池接受了新的任务 
        // 3. 线程池不在RUNNING状态，阻塞队列为空。  满足这3个条件中的任意一个的话，拒绝执行任务
        if (rs >= SHUTDOWN &&
            ! (rs == SHUTDOWN &&
               firstTask == null &&
               ! workQueue.isEmpty()))
            return false;

        for (;;) {
            int wc = workerCountOf(c); // 线程池线程个数
            if (wc >= CAPACITY ||
                wc >= (core ? corePoolSize : maximumPoolSize)) // 如果线程池线程数量超过线程池最大容量或者线程数量超过了基本大小(core参数为true，core参数为false的话判断超过最大大小)
                return false; // 超过直接返回false
            if (compareAndIncrementWorkerCount(c)) // 没有超过各种大小的话，cas操作线程池线程数量+1，cas成功的话跳出循环
                break retry;
            c = ctl.get();  // 重新检查状态
            if (runStateOf(c) != rs) // 如果状态改变了，重新循环操作
                continue retry;
            // else CAS failed due to workerCount change; retry inner loop
        }
    }
    // 走到这一步说明cas操作成功了，线程池线程数量+1
    boolean workerStarted = false; // 任务是否成功启动标识
    boolean workerAdded = false; // 任务是否添加成功标识
    Worker w = null;
    try {
        final ReentrantLock mainLock = this.mainLock; // 得到线程池的可重入锁
        w = new Worker(firstTask); // 基于任务firstTask构造worker
        final Thread t = w.thread; // 使用Worker的属性thread，这个thread是使用ThreadFactory构造出来的
        if (t != null) { // ThreadFactory构造出的Thread有可能是null，做个判断
            mainLock.lock(); // 锁住，防止并发
            try {
                // 在锁住之后再重新检测一下状态
                int c = ctl.get();
                int rs = runStateOf(c);

                if (rs < SHUTDOWN ||
                    (rs == SHUTDOWN && firstTask == null)) { // 如果线程池在RUNNING状态或者线程池在SHUTDOWN状态并且任务是个null
                    if (t.isAlive()) // 判断线程是否还活着，也就是说线程已经启动并且还没死掉
                        throw new IllegalThreadStateException(); // 如果存在已经启动并且还没死的线程，抛出异常
                    workers.add(w); // worker添加到线程池的workers属性中，是个HashSet
                    int s = workers.size(); // 得到目前线程池中的线程个数
                    if (s > largestPoolSize) // 如果线程池中的线程个数超过了线程池中的最大线程数时，更新一下这个最大线程数
                        largestPoolSize = s;
                    workerAdded = true; // 标识一下任务已经添加成功
                }
            } finally {
                mainLock.unlock(); // 解锁
            }
            if (workerAdded) { // 如果任务添加成功，运行任务，改变一下任务成功启动标识
                t.start(); // 启动线程，这里的t是Worker中的thread属性，所以相当于就是调用了Worker的run方法
                workerStarted = true;
            }
        }
    } finally {
        if (! workerStarted) // 如果任务启动失败，调用addWorkerFailed方法
            addWorkerFailed(w);
    }
    return workerStarted;
}
```


# 参考
[http://fulong258.blog.163.com/blog/static/17895044201082951820935](http://fulong258.blog.163.com/blog/static/17895044201082951820935)

[http://www.oschina.net/question/12_2656](http://www.oschina.net/question/12_2656)

[http://coach.iteye.com/blog/855850](http://coach.iteye.com/blog/855850)

[http://fangjian0423.github.io/2016/03/22/java-threadpool-analysis/](http://fangjian0423.github.io/2016/03/22/java-threadpool-analysis/)
