---
title: Kafka中的主题和分区
date: 2018-05-15 20:56:31
categories: 
- MQ
tags:
- Kafka
---

Kafka的主题和分区学习。

<!--more-->

- 主题和分区是逻辑上的概念
- 分区可以有一到多个副本，每个副本对应一个日志文件，每个日志文件对应一到多个日志分段LogSegment，每个日志分段还可细分为索引文件、日志存储文件、快照文件。

![topic-partition](/Kafka中的主题和分区/topic-partition-1.png)



- 分区使用多副本机制提升可靠性，只有leader副本对外提供读写服务，follower副本只负责在内部进行消息同步。如果一个分区的leader副本不可用，Kafka会从剩余的follower副本中挑选一个新的leader副本来继续对我提供服务

# 参考

- Apache kafka实战
- 深入理解Kafka：核心设计与实践原理