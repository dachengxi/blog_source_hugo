---
title: Redis有序集合使用和应用场景.md
date: 2019-04-20 00:20:37
categories: 
- Redis

---

有序集合Sorted Set的使用、实现、以及应用场景学习。

<!--more-->

- Redis的有序集合的底层实现使用了跳表。
- 有序集合同时使用跳表和字典实现

使用场景：

- 排行榜、时间排序的列表
- 带权重的队列
- 做交集、差集、并集，公共好友
- 延时队列
- 限流

# 参考

- 《redis设计与实现》（第二版）
- [https://zhuanlan.zhihu.com/p/147912757](https://zhuanlan.zhihu.com/p/147912757)
- [https://www.jianshu.com/p/eddd2388d077](https://www.jianshu.com/p/eddd2388d077)
- [https://cloud.tencent.com/developer/article/1402590](https://cloud.tencent.com/developer/article/1402590)
- [https://www.oraclejsq.com/redisjc/040101722.html](https://www.oraclejsq.com/redisjc/040101722.html)
- [https://juejin.cn/post/6844903951502934030](https://juejin.cn/post/6844903951502934030)