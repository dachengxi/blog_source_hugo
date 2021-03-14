---
title: Java基础之ReentrantLock简介
date: 2017-04-08 15:13:45
categories: 
- Java基础
---
Java中Lock的基础是AQS，和synchronized功能类似，而AQS和synchronized都是管程模型的实现，所以需要先熟悉管程相关知识以及AQS相关知识，才能更好地认识Lock。

<!--more-->

# Lock框架和synchronized
Lock和synchronized相比，添加了如下功能：

- 支持中断。
- 支持超时。
- 支持公平模式和非公平模式。
- 支持多个条件变量。

另外Lock需要自己显示加锁和释放锁。

如果了解了管程和AQS，Lock具体实现基本没什么可以看的，可以参考之前的几篇：Java基础之AQS设计思路介绍、Java基础之管程介绍、Java基础之自旋锁介绍。

# 源码

参照源码解析：[https://github.com/dachengxi/Java8u192SourceCode](https://github.com/dachengxi/Java8u192SourceCode)