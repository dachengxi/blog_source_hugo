---
title: Lucene中的Directory
date: 2018-06-17 16:28:23
categories: 
- Lucene
tags:
- Lucene
---

Lucene的Directory学习。

<!--more-->

Directory提供一个简单的文件类存储API，子类实现如下：

- SimpleFSDirectory，使用`java.io.*`API将文件存入文件系统，不能很好的支持多线程操作，如果要支持多线程，需要在内部加锁；不支持按位置读取
- NIOFSDirectory，使用nio API将文件保存至文件系统，能支持多线程操作
- MMapDirectory，使用内存映射进行文件访问，Java没有提供方法来取消文件在内存中的映射关系，只有在JVM进行垃圾回收时才会关闭文件和释放内存
- RAMDirectory，所有文件存入RAM
- FileSwitchDirectory，使用两个文件目录，根据文件扩展名在两个目录间切换使用



TODO

# 参考

- Lucene实战

