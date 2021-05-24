---
title: Redis内存淘汰机制
date: 2019-04-14 17:02:40
categories: 
- Redis
---

Redis内存淘汰机制学习。

<!--more-->

Redis内存淘汰策略有以下几种：

- volatile-lru，从已设置过期时间的数据集（redisDb.expires字典）中挑选最近最少使用的数据淘汰，Redis并不是将所有的最近最少使用的数据全部淘汰，而是随机挑选几个进行淘汰。
- volatile-lfu，从已设置过期时间的数据集（redisDb.expires字典）中选择使用频率最少的数据进行淘汰。
- volatile-ttl，从已设置过期时间的数据集（redisDb.expires字典）中挑选将要过期的数据进行淘汰，Redis并不是将所有的将要过期的数据全部淘汰，而是随机挑选几个进行淘汰。
- volatile-random，从已设置过期时间的数据集（redisDb.expires字典）中随机选择数据进行淘汰。
- allkeys-lru，从所有数据集（redisDb.dict字典）中挑选最近最少使用的数据淘汰。
- allkeys-lfu，从所有数据集（redisDb.dict字典）中选择使用频率最少的数据进行淘汰。
- allkeys-random，从所有数据集（redisDb.dict字典）中随机选择数据进行淘汰。
- no-enviction，禁止淘汰数据，新写入会报错。

一般会选择allkeys-lru淘汰策略，原因是如果我们的应用对缓存的访问符合幂律分布，存在相对热点数据，或者不太清楚应用的缓存访问分布状况，则可以选allkeys-lru策略。

Redis可以配置maxmemory来开启内存淘汰机制，当内存达到了配置的值，就会开始淘汰内存中的数据。maxmemory为0的时候表示对内存的使用没有限制。

内存淘汰机制有两种情况会触发：

1. 客户端的查询或者插入的命令执行时，会触发内存淘汰机制
2. 客户端修改maxmemory的配置的时候会触发

这两种情况都会调用freeMemoryIfNeededAndSafe方法触发。