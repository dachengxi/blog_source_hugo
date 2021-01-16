---
title: MyBatis中Executor介绍
date: 2019-06-23 18:10:02
categories: MyBatis
tags: 
- MyBatis
---

在MyBatis的执行流程中，SqlSession会使用Executor来执行实际的操作，Executor中定义了操作数据库的基本方法。Executor的具体实现再使用StatementHandler来执行更底层操作。Executor是核心组件之一。

<!--more-->

Executor共有四种：

- SimpleExecutor，是默认的执行器，它每次执行完一个操作，就会把使用的Statement关闭。
- ReuseExecutor，可重用执行器，同一个SqlSession每次执行完一个操作，会把使用到的Statement缓存起来，可以重用Statement。ReuseExecutor维护着一个statementMap，key是执行的sql，value是对应的Statement对象，相同的sql就可以重用Statement对象。
- BatchExecutor，可以批量的执行修改，每次的修改操作记录到内存中，等待事务提交的时候或者触发下一次查询的时候，批量提交修改操作。
- CachingExecutor，缓存执行器，查询的时候会先从缓存中查询。如果MyBatis开启了二级缓存，才会使用CachingExecutor，使用装饰器模式，内部持有的还是上面三种Executor实现。