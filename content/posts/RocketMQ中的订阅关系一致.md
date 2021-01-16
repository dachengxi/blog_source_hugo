---
title: RocketMQ中的订阅关系一致
date: 2018-01-22 20:58:04
categories: 
- MQ
tags:
- RocketMQ
---

RocketMQ的订阅关系一致学习。

<!--more-->

订阅关系一致是指同一个消费者组下所有的消费者实例处理逻辑必须完全一致，所有消费者订阅的Topic以及Topic中的Tag必须一致。如果订阅关系不一致，消费逻辑就会混乱，导致消息丢失。

因为在Broker中消费者订阅信息是以消费组为维度存放的，如果一个消费组中的的消费者订阅不一样，后面的消费者会覆盖前面消费者在消费组中的订阅信息。

可参考：[RocketMQ中Consumer的心跳发送][https://www.cxis.me/2018/01/23/RocketMQ中Consumer的心跳发送/]这篇文章中Consumer心跳发送和Broker中处理心跳的流程

# 参考

- [https://www.alibabacloud.com/help/zh/doc-detail/43523.htm]( https://www.alibabacloud.com/help/zh/doc-detail/43523.htm)