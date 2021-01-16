---
title: DynamicConfigCenter设计的思路
date: 2020-04-25 20:07:40
categories: 
- DynamicConfigCenter
tags:
- DynamicConfigCenter
---



大概列一下动态配置中心需要实现的功能。

<!--more-->

- 配置增删改查
- 环境隔离
- 客户端缓存
- 基于Zookeeper
- 集成Spring

源码：[https://github.com/dachengxi/DynamicConfigCenter](https://github.com/dachengxi/DynamicConfigCenter)

关于具体的思路和实现可以参考下面的文章。

# 参考

- [https://juejin.im/entry/5af3a38ff265da0b9e6511c7](https://juejin.im/entry/5af3a38ff265da0b9e6511c7)
- [http://jm.taobao.org/2016/09/28/an-article-about-config-center/](http://jm.taobao.org/2016/09/28/an-article-about-config-center/)
- [https://cloud.tencent.com/developer/article/1063232](https://cloud.tencent.com/developer/article/1063232)
- [https://disconf.readthedocs.io/zh_CN/latest/design/src/分布式配置管理平台Disconf.html](https://disconf.readthedocs.io/zh_CN/latest/design/src/分布式配置管理平台Disconf.html)
- [https://www.sfanonline.cn/Nacos配置中心设计思想/](https://www.sfanonline.cn/Nacos配置中心设计思想/)
- [https://www.cnblogs.com/hujunzheng/p/10932153.html](https://www.cnblogs.com/hujunzheng/p/10932153.html)
- [https://dong4j.github.io/views/middleware/2017/03291001.html](https://dong4j.github.io/views/middleware/2017/03291001.html)

