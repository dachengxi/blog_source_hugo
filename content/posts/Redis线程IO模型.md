---
title: Redis线程IO模型
date: 2019-04-03 23:10:00
categories: 
- redis
---

Redis单线程IO模型：基于Reactor模式，采用IO多路复用处理请求，单线程运行，高性能。

<!--more-->

# 参考

- 《redis设计与实现》（第二版）
- [https://strikefreedom.top/multiple-threaded-network-model-in-redis](https://strikefreedom.top/multiple-threaded-network-model-in-redis)
- [https://www.cnblogs.com/liang24/p/14178730.html](https://www.cnblogs.com/liang24/p/14178730.html)