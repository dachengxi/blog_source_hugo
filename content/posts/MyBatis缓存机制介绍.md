---
title: MyBatis缓存机制介绍
date: 2019-07-06 21:58:17
categories: MyBatis
tags: 
- MyBatis
---

MyBatis提供一级缓存和二级缓存，默认一级缓存是开启的，二级缓存是需要手动开启的。MyBatis的一级缓存是SqlSession级别的，二级缓存是mapper级别的，多个SqlSession可共享同一个二级缓存。如果开始了二级缓存，查询数据会先查询二级缓存，在查询一级缓存，最后才是查库。

<!--more-->

# 一级缓存

一级缓存是SqlSession级别的，SqlSession会持有Executor，使用Executor来进行真正的操作，在BaseExecutor中有个localCache对象，就是实现一级缓存的。

一级缓存有两种：Session级别的和Statement级别的，默认就是Session级别，Statement级别的缓存只针对一次Statement有效。

多个SqlSession并发的情况下，写数据库可能会引起脏数据。

SqlSession执行了插入、更新、删除操作，就会清空一级缓存。

# 二级缓存

二级缓存是多个SqlSession共享的，mapper级别的缓存，默认没有开启，需要手动开启。开启后，SqlSession会持有一个CachingExecutor，这个Executor使用装饰器模式，增加了二级缓存功能。

二级缓存在分布式环境下是很不安全的，因为它是单机的缓存，分布式环境会导致读取到脏数据。

# MyBatis整合Spring后的一级缓存

Spring整合MyBatis后，MyBatis将SqlSession交给了Spring管理，如果说不是同一个事务每次操作都是新的SqlSession，不会命中一级缓存；如果是同一个事务，可以使用同一个SqlSession。