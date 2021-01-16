---
title: Redis内存淘汰机制
date: 2019-04-14 17:02:40
categories: 
- 缓存
tags:
- redis
---

Redis可以设置key的过期时间，key过期了之后会根据key过期策略将key进行淘汰，但是如果Redis的内存到达了一定的阈值，即使key没有过期，也会根据一定的策略对数据进行淘汰。

<!--more-->

Redis可以配置maxmemory来开启内存淘汰机制，当内存达到了配置的值，就会开始淘汰内存中的数据。maxmemory为0的时候表示对内存的使用没有限制。

Redis内存淘汰策略有以下几种：

- volatile-lru，从已设置过期时间的数据集中挑选最近最少使用的数据淘汰，设置过期时间的数据在expires字典中，Redis并不是将所有的最近最少使用的数据全部淘汰，而是随机挑选几个进行淘汰。
- volatile-ttl，从已设置过期时间的数据集中挑选将要过期的数据进行淘汰，设置过期时间的数据在expires字典中，Redis并不是将所有的将要过期的数据全部淘汰，而是随机挑选几个进行淘汰。
- volatile-random，从已设置过期时间的数据集中随机选择数据进行淘汰。
- allkeys-lru，从所有数据集中挑选最近最少使用的数据淘汰，数据集在服务器的dict字典中。
- allkeys-random，从所有数据集中随机选择数据进行淘汰，数据集在服务器的dict字典中。
- no-enviction，禁止淘汰数据，新写入会报错。

Redis4.0之后增加了两个LFU（Least Frequently Used）使用频率最少的：

- volatile-lfu，从已设置过期时间的数据集中选择使用频率最少的数据进行淘汰。
- allkeys-lfu，从所有数据集中选择使用频率最少的数据进行淘汰。

一般会选择allkeys-lru淘汰策略，原因是如果我们的应用对缓存的访问符合幂律分布，存在相对热点数据，或者不太清楚应用的缓存访问分布状况，则可以选allkeys-lru策略。