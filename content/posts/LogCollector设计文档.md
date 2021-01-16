---
title: LogCollector设计文档
date: 2010-05-20 17:30:52
categories: 
- LogCollector
tags:
- LogCollector
---

LogCollector的模块和整体架构。

<!--more-->

- LogCollector-client，需要应用依赖此库，用来收集JVM等日志信息转发到localAgent上
- LogCollector-localAgent，作为Kafka的消息发送端，将接收到的日志发送到server上、监听日志文件收集日志文件发送到Kafka
- LogCollector-server，处理收到的消息，可进行业务处理和分析，存储到Elasticsearch中

# 整体架构

使用drawio画图，这是源文件：[LogCollector整体架构](/LogCollector设计文档/LogCollector整体架构.drawio)

![](/LogCollector设计文档/LogCollector整体架构.png)

# 参考

- [https://www.jianshu.com/p/8b45af25cbe9](https://www.jianshu.com/p/8b45af25cbe9)
- [https://www.jianshu.com/p/63d7d4d0e598](https://www.jianshu.com/p/63d7d4d0e598)
- [https://www.cnblogs.com/wzj4858/p/8252730.html](https://www.cnblogs.com/wzj4858/p/8252730.html)