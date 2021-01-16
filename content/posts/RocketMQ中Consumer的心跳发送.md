---
title: RocketMQ中Consumer的心跳发送
date: 2018-01-23 11:58:22
categories: 
- MQ
tags:
- RocketMQ
---

RocketMQ Consumer的心跳发送学习。

<!--more-->

Consumer和Broker保持长连接，Consumer会向Broker发送心跳信息，心跳信息中包含了当前消费者的订阅信息。

# 发送心跳的场景

- 在DefaultMQPushConsumerImpl#start启动后会发送心跳
- 在DefaultMQPushConsumerImpl#subscribe订阅topic和tag的时候会发送心跳
- 在RebalancePushImpl#messageQueueChanged消息队列有变化后会发送心跳
- 在MQClientInstance#startScheduledTask启动定时任务时会启动一个定时任务发送心跳
- 在DefaultMQProducerImpl#start启动后会发送心跳

# 发送和处理过程

- Consumer向每个Broker发送心跳，包含消费者订阅信息
- Broker处理心跳请求
- 获取消费组订阅配置信息，不存在就新建，并持久化
- 注册重试队列，新注册后会向Nameserver广播请求更新路由信息
- 注册消费者到消费者表中，没有则新增消费者，新增完消费者后需要通知所有消费者进行rebalance
- 注册生产者

# 订阅关系一致性

一个消费组中的所有消费者订阅的信息必须完全一致，如果不一致的话回产生消费混乱，丢失消息。因为在Broker中消费者订阅信息是以消费组为维度存放的，如果一个消费组中的的消费者订阅不一样，后面的消费者会覆盖前面消费者在消费组中的订阅信息。

# draw io源文件和图片

draw io源文件：[RocketMQ中Consumer的心跳发送.drawio](/RocketMQ中Consumer的心跳发送/RocketMQ中Consumer的心跳发送.drawio)

![RocketMQ中Consumer的心跳发送](/RocketMQ中Consumer的心跳发送/RocketMQ中Consumer的心跳发送.png)

# 参考

