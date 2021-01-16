---
title: Kafka中的副本
date: 2018-05-23 20:14:08
categories: 
- MQ
tags:
- Kafka
---

Kafka的副本学习。

<!--more-->

Kafka分区本质上是一个备份日志，利用多份相同的备份共同提供冗余机制来保持系统高可用性。这些备份称为副本replica。

Kafka会把分区的所有副本均匀的分配到所有Broker上，并从这些副本中选一个作为Leader对外提供读写服务，其他的副本是Follower副本，只能向Leader请求数据，保持与Leader的同步。

如果Leader副本宕机，Follower副本会竞争成为新的Leader，但不是所有的Follower都有资格去成为Leader，对于落后Leader太多的是没有资格竞选Leader的。

# ISR

ISR in-sync replicas，Kafka集群动态维护一组同步副本集合，每个Topic分区都有自己的ISR列表，ISR中所有副本都与Leader保持同步状态，Leader副本总是在ISR列表中。

只有在ISR中的副本才有资格选举为Leader。Producer写入的一条消息只有被ISR中所有副本都接收到，才被视为已提交。

# 参考

- Apache kafka实战
- 深入理解Kafka：核心设计与实践原理