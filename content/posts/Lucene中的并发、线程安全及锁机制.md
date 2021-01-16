---
title: Lucene中的并发、线程安全及锁机制
date: 2018-06-17 16:28:23
categories: 
- Lucene
tags:
- Lucene
---

Lucene中的并发、线程安全及锁机制学习。

<!--more-->

# 线程安全和多虚拟机安全

- 任意数量的只读的IndexReader都可以同时打开一个索引同一个索引
- 对于一个索引，一次只能有一个Writer打开它。Lucene采用文件锁来保障，一旦建立起IndexWriter对象，系统会分配一个锁给它
- IndexReader对象可以在IndexWriter对象正在修改索引时打开索引
- 任意多个线程可以共享同一个IndexReader或IndexWriter

# 锁机制

Lucene采用基于文件的锁。

- NativeFSLockFactory，FSDirectory的默认锁，使用nio本地操作系统锁。
- SimpleFSLockFactory，使用Java的File.createNewFile API
- SingleInstanceLockFactory，在内存中创建一个完全的锁，是RAMDirectory默认的锁实现
- NoLockFactory，关闭锁机制

这些锁的实现都是不公平的。

# 参考

- Lucene实战

