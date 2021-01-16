---
title: MySQL的行锁、间隙锁和next-key lock
date: 2018-04-18 19:29:02
categories: MySQL
tags: 
- MySQL


---

MySQL的行锁、间隙锁和next-key lock学习。

<!--more-->

# 行锁

Record Lock，行锁，InnoDB行锁是针对索引进行加锁，不是对记录加锁，如果索引失效就会从行锁升级为表锁。

使用行锁开销大，加锁慢，会出现死锁，但是行锁的锁粒度小，并发度高。

加锁的方式：

- 自动加锁，update、delete、insert操作，InnoDB引擎都会自动给涉及到的数据加排它锁，select语句不会加锁。
- 显示加锁，使用select...lock in share mode或select...for update

# 间隙锁

间隙锁，锁定索引记录间隙，确保索引记录间隙不变。间隙锁在Repeatable Read以上的隔离级别才可用。间隙锁为了解决同一事务中出现幻读的问题。

自动使用间隙锁的条件：

- 必须在Repeatable Read级别下
- 检索条件必须有索引，如果没有索引会变成表锁锁定整张表。

# Next-Key Lock

行锁和间隙锁组合起来叫做Next-Key Lock。

InnoDB引擎中Repeatable Read隔离级别下也不会出现幻读，是用的就是Next-Key Lock（行锁加间隙锁）来解决的。当InnoDB扫描索引记录的时候，先对索引记录加上行锁，再对索引记录两边的间隙加上间隙锁，之后其他事务就不能在这个间隙修改或者插入记录。

Next-Key Lock是行锁和间隙锁组合，对数据进行条件检索、范围检索时，对其范围内间隙进行加锁。InnoDB对行的查询都采用Next-Key Lock的算法，但当查询的索引含有唯一属性（唯一索引、主键索引）时，InnoDB会给Next-Key Lock进行优化，降为行锁，仅锁住索引本身，而不是范围。如果是普通辅助索引，会使用Next-Key Lock进行范围锁定。

# 幻读解决

- 快照读，使用MVCC解决幻读
- 当前读，依赖间隙锁解决幻读

# 参考

- [https://www.cnblogs.com/itdragon/p/8194622.html](https://www.cnblogs.com/itdragon/p/8194622.html)
- [https://www.cnblogs.com/zhoujinyi/p/3435982.html](https://www.cnblogs.com/zhoujinyi/p/3435982.html)
- [https://blog.csdn.net/liqfyiyi/article/details/72771845](https://blog.csdn.net/liqfyiyi/article/details/72771845)
- [https://www.cnblogs.com/aspirant/p/9177978.html](https://www.cnblogs.com/aspirant/p/9177978.html)