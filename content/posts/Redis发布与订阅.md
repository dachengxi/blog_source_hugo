---
title: Redis发布与订阅
date: 2019-04-14 14:08:13
categories: 
- Redis
---

Redis支持发布订阅功能，SUBSCRIBE命令可以让客户端订阅一个或者多个频道，PSUBSCRIBE可以让客户端订阅一个或多个模式的，PUBLISH用来发布消息。

<!--more-->

# 频道的订阅和退订

客户端执行SUBSCRIBE可以订阅频道，订阅关系保存在服务器状态的pubsub_channels字典里：

- 键时某个被订阅的频道
- 值是一个链表，记录了所有订阅这个频道的客户端

退订频道的时候使用UNSUBSCRIBE命令，服务器会把客户端从pubsub_channels字典中删除。

# 发送消息

客户端使用`PUBLISH <channel> <message>`来发送消息给频道channel，服务器会执行：

- 将消息发送给所有订阅了这个channel的订阅者
- 如果有模式频道匹配到，则将消息发送给订阅了这个模式的订阅者

# 参考

- 《redis设计与实现》（第二版）