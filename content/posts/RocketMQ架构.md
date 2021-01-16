---
title: RocketMQ架构
date: 2018-01-01 14:05:21
categories: 
- MQ
tags:
- RocketMQ
---

RocketMQ包括：NameServer、Broker、Producer、Consumer。

<!--more-->

![](/RocketMQ架构/rocketmq-architecture.jpg)

# NameServer

- NameServer存储Topic、Broker关系信息，NameServer间没有通信，单台宕机不影响其他NameServer和集群，整个NameServer集群宕机，已正常工作的Producer、Consumer、Broker能正常工作，新起来的就无法工作。
- 管理brokers，Broker服务启动时，会注册到NameServer上，两者保持心跳检测机制，保证NameServer知道Broker的存活。
- NameServer存有全部Broker集群信息和生产者消费者客户端的请求信息。

# Broker

- 负责存储消息、转发消息。
- Broker节点与所有的NameServer节点保持长连接和心跳，定时将Topic信息注册到NameServer上。

# Producer

- 消息生产者，负责产生消息

# Consumer

- 消息消费者，消费消息
- 支持PUSH和PULL模式，支持集群消费和广播消费