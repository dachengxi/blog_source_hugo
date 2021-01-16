---
title: RocketMQ中的消息存储
date: 2018-01-06 10:31:59
categories: 
- MQ
tags:
- RocketMQ
---

RocketMQ的消息存储学习。

<!--more-->

RocketMQ存储主要文件有：

- Commitlog，存储消息，顺序写文件，所有Topic的消息都存储在Commitlog文件中
- ConsumeQueue，消息消费队列文件，每个Topic包含多个消息消费队列，每个消息队列有一个消息文件，消息写入Commitlog文件后，异步转发到消息队列中
- IndexFile，索引文件，为了加速消息的检索性能，根据消息属性快速从Commitlog文件中检索消息，主要存储key和offset对应关系

事务状态服务，存储每条消息的事务状态。

定时消息服务，每个延迟级别对应一个消息消费队列，存储延迟队列的消息拉取进度。

# 消息存储流程

- 如果当前Broker不可用、Broker为SLAVE角色、当前不支持写入、消息Topic长度超过256个字符、属性长度超过65536字符，则拒绝写入
- 如果消息延迟级别大于0，将消息原来Topic名称和原来消息ID存入消息属性中，用延迟消息主题SCHEDULE_TOPIC、消息队列ID更新原来主题和队列
- 获取当前可写入的Commitlog文件
-  写入Commitlog之前先申请putMessageLock锁
- 设置消息存储时间，如果mappedFile为空，说明本次消息是第一次发送，使用偏移量0创建第一个commit文件
- 将消息追加到MappedFile中
- 创建全局唯一消息ID，消息ID组成：4字节ip，4字节端口号，8字节消息偏移量
- 获取该消息在消息队列的偏移量
- 根据消息体长度、主题长度、属性长度计算消息总长度
- 如果消息长度+END_FILE_MIN_BLANK_LENGTH大于Commitlog文件空闲空间，返回END_OF_FILE，Broker会重新创建一个新的Commitlog文件来存储该消息
- 消息内容存储到ByteBuffer中，然后创建AppendMessageResult，这里只是将消息存储在MappedFile对应的内存Buffer中，没有刷盘。
- 根据同步刷盘还是异步刷盘，将内存中数据持久化到磁盘

# Commitlog文件

Commitlog文件存储在`${ROCKET_HOME}/store/commitlog/`，每个文件默认1G。