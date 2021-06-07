---
title: Redis有序集合使用和应用场景.md
date: 2019-04-20 00:20:37
categories: 
- Redis

---

有序集合Sorted Set的使用、实现、以及应用场景学习。

<!--more-->

有序集合Sorted Set，zset，是一个有顺序的Set。

# 用法

| 命令             | 描述                                                         | 用法                                                         | 备注                                                         |
| ---------------- | ------------------------------------------------------------ | ------------------------------------------------------------ | ------------------------------------------------------------ |
| zadd             | 添加一个或多个元素到key中                                    | `zadd key socre member [[score member] [score member] ...]`  | 一个key可以有多个member，每个member都可以指定score值         |
| zrem             | 将key中一个或者多个元素移除掉                                | `zrem key member [member ...]`                               |                                                              |
| zcard            | 返回key下面member的个数                                      | `zcard key`                                                  |                                                              |
| zcount           | 返回key的score在min和max之间的member的个数                   | `zcount key min max`                                         |                                                              |
| zscore           | 返回指定的key下member的socre                                 | `zscore key member`                                          |                                                              |
| zincrby          | 为指定key的member的socre增加increment                        | `zincrby key increment member`                               | increment如果是负数，表示减法；如果key或者member不存在，则和zadd一样添加元素 |
| zrange           | 返回key下某个区间的member                                    | `zrange key start stop [withscores]`                         | 默认按照score从小到大排列                                    |
| zrevrange        | 返回key下某个区间的member                                    | `zrevrange key start stop [withsocres]`                      | 排序按照socre从大到小                                        |
| zrangebyscore    | 返回socre在某个区间的member                                  | `zrangebyscore key min max [withscores] [limit offset count]` | 默认按照socre从小到大排列；limit可指定返回结果的区间和数量；min和max可以有开闭区间的概念 |
| zrevrangebyscore | 返回socre在某个区间的member                                  | `zrevrangebysocre key max min [withscores] [limit offset count]` | 排序按照score从大到小                                        |
| zrank            | 返回key下member的排名                                        | `zrank key member`                                           | 按照socre从小到大排列                                        |
| zrevrank         | 返回key下member的排名                                        | `zrevrank key member`                                        | 按照socre从大到小排列                                        |
| zremrangebyrank  | 移除指定key的某个排名区间的member                            | `zremrangebyrank key start stop`                             |                                                              |
| zremrangebyscore | 移除指定key的某个socre区间的member                           | `zremrangebyscore key min max`                               |                                                              |
| zinterstore      | 将指定的若干个key中相同的member的分数相加，并放到一个新的key下面/（求交集） | `zinterstore new_key keys_number key [key ...]`              |                                                              |
| zunionstore      | 求并集                                                       | `zunionstore new_key keys_number key [key ...]`              |                                                              |

# 实现

zset通过添加一个score参数来为集合中元素进行排序。底层同时使用了跳表和字典来实现，zset结构如下：

```c
typedef struct zset {
    // 保存了member到score的映射
    dict *dict;
    // 按照socre从小到大包存了所有集合元素
    zskiplist *zsl;
} zset;
```

zset中跳表保存了所有的集合元素，按照score从小到大排列。通过跳表，可以对集合进行范围型的操作，比如zrank、zrange等。

zset中字典保存了member到score的映射，字典中元素的key是member，值是score，可以用O(1)的复杂度查找给定member的score，比如zscore命令。

# 使用场景

- 排行榜、时间排序的列表
- 带权重的队列
- 做交集、差集、并集，公共好友
- 延时队列
- 限流

## 排行榜

将点击数、评论数等作为score，帖子id作为member，可以使用zrange或者zrevrange来获取到排行数据。

## 延时队列

可以将时间戳当作score，将元素插入到集合中，zset会按照score进行排序，这样集合中元素就按照时间戳从旧到新进行排序，可以使用死循环一直取第一个key和当前时间戳进行比较，如果当前时间戳大于key的score，则可以将它取出来进行消费删除。

## 交叉并集、求公共好友

可利用redis的zset的交叉并来实现

## 限流

使用用户id作为key，访问的时间戳作为member或者score，统计key下面指定时间戳区间内个数，就能得到滑动窗口内访问频次，通过与最大次数比较，来决定这次是否允许通过。

## 带权重队列

设置普通消息score是1，重要消息score是2，工作线程可以按照score倒序来获取工作，让重要任务先执行。

# 参考

- 《redis设计与实现》（第二版）
- [https://zhuanlan.zhihu.com/p/147912757](https://zhuanlan.zhihu.com/p/147912757)
- [https://www.jianshu.com/p/eddd2388d077](https://www.jianshu.com/p/eddd2388d077)
- [https://cloud.tencent.com/developer/article/1402590](https://cloud.tencent.com/developer/article/1402590)
- [https://www.oraclejsq.com/redisjc/040101722.html](https://www.oraclejsq.com/redisjc/040101722.html)
- [https://juejin.cn/post/6844903951502934030](https://juejin.cn/post/6844903951502934030)