---
title: JVM四种引用类型
date: 2017-06-18 22:01:48
categories: 
- JVM
tags:
- JVM
---

Java提供了四种引用类型：强引用、软引用、弱引用、虚引用，这几种不同引用类型主要体现在GC上。

<!--more-->

- 强引用，StrongReference，默认引用形式，我们通过new关键字创建一个对象，就是强引用。一个对象具有强引用，不会被垃圾回收器回收，内存不足的时候即使跑出OutOfMemoryError也不会回收强引用。
- 软引用，SoftReference，如果内存足够，软引用不会被回收，如果内存不足，软引用就会被垃圾回收器回收，可用来实现缓存。
- 弱引用，WeakReference，当JVM进行垃圾回收的时候发现有弱引用，不管空间是否足够用，都会回收弱引用。
- 虚引用，PhantomReference，任何时候都能被垃圾回收器回收。

