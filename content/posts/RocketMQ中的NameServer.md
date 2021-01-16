---
title: RocketMQ中的NameServer
date: 2018-01-01 17:39:10
categories: 
- MQ
tags:
- RocketMQ
---

RocketMQ的NameServer学习。

<!--more-->



NameServer存储Topic和Broker的信息，Broker启动的时候会向所有的NameServer注册。Producer在发送消息之前会先从NameServer获取Broker地址列表，按照负载均衡算法从列表中选择一台Broker服务器发送消息。

NameServer和Broker保持长连接，每隔30s检测Broker是否存活，如果Broker宕机，从路由注册表中删除。路由变化不会马上通知Producer，这样实现降低了NameServer实现复杂度，Producer会通过容错机制来保证消息发送高可用。

# NameServer路由注册

NameServer主要存储了：

- topicQueueTable，Topic队列路由信息，发消息时根据路由表进行负载均衡
- borkerAddrTable，Broker基础信息，包含Broker名字、所属集群名称、主备Broker地址
- clusterAddrTable，Broker集群信息，存储集群中所有Broker名称
- brokerLiveTable，Broker状态信息，NameServer每次收到心跳包就会更新该状态信息
- filterServerTable，Broker上的FilterServer列表，用于类模式消息过滤

路由注册通过Broker和NameServer的心跳功能实现，Broker启动时，向集群中所有NameServer发送心跳，每隔30秒向集群所有NameServer发心跳包。

NameServer收到心跳包后，更新brokerLiveTable缓存中的信息。

NameServer每隔10秒扫描brokerLiveTable，如果连续120s没有收到心跳包，NameServer会移除Broker的路由信息，同时关闭Socket连接。

NameServer与Broker保持长连接。

# NameServer路由删除

Broker每隔30s向所有NameServer发送心跳包，NameServer每隔10s扫描brokerLiveTable状态，如果超过120秒Broker状态没更新，认为Broker失效，移除Broker并且关闭与Broker的连接。

Broker如果是正常关闭，会触发路由删除。

# 路由发现

Topic路由发生变化后，NameServer不会主动推送给客户端，而是由客户端定时拉取Topic最新路由。根据Topic名称拉取路由信息。

路由信息包括：

- orderTopicConfig，顺序消息配置内容
- queueData，topic队列元数据
- brokerDatas，topic分布的Broker元数据
