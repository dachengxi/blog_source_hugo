---
title: DynamicConfigCenter中Zookeeper监听机制使用
date: 2020-04-28 09:51:52
categories: 
- DynamicConfigCenter
tags:
- DynamicConfigCenter
---

重新温习一下Zookeeper监听机制。

<!--more-->

Zookeeper节点可被监控，包括节点数据修改、子节点变化。

Watch机制是一次性的，一次事件监听完成后，如果需要继续监听，需要重新注册。

Watch通知事件是异步的，但是Zookeeper保证一个客户端在看到Watch事件前是不会看到节点数据变化的。

Zookeeper中所有的读操作都可以设置监听：getData()、getChildren()、exists()。

# Zookeeper事件

- Created event，创建事件，如果调用exists方法设置了监听，将会接到这个创建事件
- Deleted event，删除事件，如果调用exists、getData、getChildren方法时设置了监听，将会接收到这个删除事件
- Changed event，变更事件，如果调用exists、getData方法时设置了监听，将会接收到这个变更事件
- Child event，子节点事件，如果调用getChildren方法时设置了监听，将会接收到这个子节点事件
- Child Remove event，子节点监听移除事件，如果调用getChildren方法的时候注册了一个Watcher，之后又取消了这个监听，将会收到一个子节点监听移除事件
- Data Remove event，数据监听移除事件，如果调用exists、getData方法的时候注册了一个Watcher，之后又取消了这个监听，将会收到一个数据监听移除事件

## 操作和事件

- create("/path")，对于exists("/path")的监听会产生一个EventType.NodeCreated事件
- delete("/path")，对于exists("/path")、getData("/path")、getChildren("/path")的监听会产生一个EventType.NodeDeleted事件
- setData("/path")，对于exists("/path")、getData("/path")的监听会产生一个EventType.NodeDataChanged事件
- create("/path/child")，对于getChildren("/path")的监听会产生一个EventType.NodeChildrenChanged事件；对于exists("/path/child")的监听会产生一个EventType.NodeCreated事件
- delete("/path/child")，对于getChildren("/path")的监听会产生一个EventType.NodeChildrenChanged事件；对于exists("/path/child")、getData("/path/child")、getChildren("/path/child")的监听会产生一个EventType.NodeDeleted事件
- setData("/path/child")，对于"/path"的监听不会产生事件，因为修改子节点的数据，对父节点无影响；对于exists("/path/child")、getData("/path/child")的监听会产生一个EventType.NodeDataChanged事件

# Curator监听

## Cutator的Watcher包装

- NodeCache，监听一个节点，监听事件为指定路径的增、删、改操作
- PathChildrenCache，监听指定路径的一级子目录，不监听该路径，监听子目录的增、删、改操作
- TreeCache，综合NodeCache和PathChildrenCache，对整个目录进行监听，可设置监听深度

## Listener

- ConnectionStateListener，连接状态监听器
- CuratorListener，主要针对background异步通知和一些错误通知，配合inBackground使用
- NodeCacheListener，配合NodeCache使用
- PathChildrenCacheListener，配合PathChildrenCache使用
- TreeCacheListener，配合TreeCache使用

源码：[https://github.com/dachengxi/DynamicConfigCenter](https://github.com/dachengxi/DynamicConfigCenter)