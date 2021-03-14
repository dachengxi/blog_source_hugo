---
title: Java基础之CountDownLatch简介
date: 2016-06-01 14:26:33
categories: 
- Java基础
---

CountDownLatch功能类似一个倒数的计数器，等到减为0才可以继续往下走，否则会一直等待计数器countDown。CountDownLatch实现还是依赖AQS。

<!--more-->

# 使用示例

CountDownLatch一般使用场景是：主线程分发任务给其他线程去处理，然后主线程调用await等待其他线程处理完任务，其他线程处理完任务后会countDown，等到所有的线程都处理完了，主线程在await处继续执行，下面是JDK中的示例：

```java
class Driver { // ...
   void main() throws InterruptedException {
     CountDownLatch startSignal = new CountDownLatch(1);
     CountDownLatch doneSignal = new CountDownLatch(N);

     for (int i = 0; i < N; ++i) // create and start threads
       new Thread(new Worker(startSignal, doneSignal)).start();

     doSomethingElse();            // don't let run yet
     startSignal.countDown();      // let all threads proceed
     doSomethingElse();
     doneSignal.await();           // wait for all to finish
   }
 }

 class Worker implements Runnable {
   private final CountDownLatch startSignal;
   private final CountDownLatch doneSignal;
   Worker(CountDownLatch startSignal, CountDownLatch doneSignal) {
     this.startSignal = startSignal;
     this.doneSignal = doneSignal;
   }
   public void run() {
     try {
       startSignal.await();
       doWork();
       doneSignal.countDown();
     } catch (InterruptedException ex) {} // return;
   }

   void doWork() { ... }
 }
```



# 实现原理

CountDownLatch基于AQS实现，实例化的时候首先初始化state的值。调用await方法的时候，需要等待，进入AQS同步队列中并阻塞住。其他线程调用countDown的时候会将state减1，直到最后一个线程countDown后state变成了0，AQS中的doReleaseShared方法进行唤醒同步队列中等待的线程，其实现在线程里面只有一个等待的节点。

# 源码分析

参照源码解析：[https://github.com/dachengxi/Java8u192SourceCode](https://github.com/dachengxi/Java8u192SourceCode)

# 参考