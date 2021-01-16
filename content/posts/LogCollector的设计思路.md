---
title: LogCollector的设计思路
date: 2010-05-20 16:44:11
categories: 
- LogCollector
tags:
- LogCollector

---

大概列一下日志收集系统需要实现的功能。

<!--more-->

日志收集方案有很多种解决方案，日志收集工具也比较多，比如Logstash、Filebeat、Flume、Scribe等等。具体的可查看参考文档和网上一系列的文章。

要自己实现一套日志收集的系统，现有的工具可以作为参考，根据实际情况进行开发，下面大概列一下日志收集系统实现需要的东西，做一个简单的日志收集系统。

- 系统日志收集，应用心跳收集、可实现为一个库，应用程序需要依赖此库
- 日志收集的代理，需要部署在和应用相同的服务器上，应用日志收集、接收和转发应用发来的心跳和日志
- 依赖Kafka，日志收集代理会把消息发送到Kafka上
- 日志收集服务器，作为Kafka的消费者，消费日志消息，并可进行加工处理、转发消息
- 依赖Elasticsearch，日志收集服务器可将日志存储到Elasticsearch

# 参考

- [https://www.jianshu.com/p/8b45af25cbe9](https://www.jianshu.com/p/8b45af25cbe9)
- [https://www.jianshu.com/p/63d7d4d0e598](https://www.jianshu.com/p/63d7d4d0e598)
- [https://www.cnblogs.com/wzj4858/p/8252730.html](https://www.cnblogs.com/wzj4858/p/8252730.html)