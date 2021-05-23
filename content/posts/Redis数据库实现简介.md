---
title: Redis数据库实现简介
date: 2019-03-25 22:10:07
categories: 
- Redis
---

Redis数据库实现学习。

<!--more-->

- redisServer结构表示redis服务器的状态
- redisDb结构表示redis服务器中的数据库
- client结构表示各个客户端的状态

Redis中redisServer结构表示服务器的状态，服务器的所有数据库存在redisServer的db数组中，数组中每个redisDb代表一个数据库，redisServer结构如下

```c
struct redisServer {
  // db数组，保存服务器中所有数据库
  redisDb *db;
  
  ...
  
  // 服务器的数据库数量
  // 服务器初始化时，程序会根据dbnum属性来决定创建多少个数据库，默认16个
  int dbnum;
}
```

每个redis客户端都有自己的目标数据库，默认是0号数据库，可以通过select命令切换客户端对应的数据库。在redis内部使用client结构来保存客户端状态，client结构中的db属性记录了当前客户端的目标数据库，client结构如下：

```c
typedef struct client {
    /**
     * db记录了客户端当前的目标数据库
     * 是指向redisDb结构的指针
     */
    redisDb *db; 
}
```

在客户端中有一个指向服务器数据库的指针。

# 参考

- 《redis设计与实现》（第二版）