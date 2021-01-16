---
title: MySQL中的锁
date: 2018-04-16 20:42:41
categories: MySQL
tags: 
- MySQL

---

MySQL锁学习。

<!--more-->

# 共享锁和排它锁

- 共享锁（读锁），读取数据时的锁，其他事务可以并发读数据，但不能写
- 排它锁（写锁），写数据时的锁，其他事务既不能读，也不能写

# MyISAM和InnoDB的锁粒度

## 锁粒度

- 表级锁，table-level locking
- 页面锁，page-level locking
- 行级锁，row-level locking

## MyISAM的锁粒度：表级锁

MyISAM引擎使用的是表级锁，可以设置读锁和写锁的优先级来避免线程一直得不到执行。

MyISAM会自动给select操作和update、delete、insert操作涉及的表添加读锁和写锁。

## InnoDB的锁粒度：表级锁和行级锁

InnoDB引擎支持行级锁和表级锁，默认是用的是行级锁。

### InnoDB行级锁

- 共享锁（S），允许当前事务和其他事务读取同一行数据，但其他事务不能修改数据
- 排它锁（X），允许获得排它锁的事务更新数据，其他事务不能读和写该数据

InnoDB行锁是针对索引加的锁，不是针对记录加锁，只有在通过索引检索数据的时候才使用行级锁，如果不通过索引检索数据则使用表级锁。

### InnoDB意向锁

意向锁是一种不与行级锁冲突的表级锁。InnoDB支持多粒度锁，允许行级锁和表级锁共存。

- 意向共享锁（IS），事务给数据行加行共享锁前，必须先取得该表的意向共享锁
- 意向排它锁（IX），事务给数据行加行排它锁前，必须先取得该表的意向排它锁

InnoDB意向锁是InnoDB引擎自动加的，不需要用户显式指定。update、delete、insert操作InnoDB会自动添加排它锁。select操作InnoDB不会加锁，需要用户显式加锁：

- 给select添加共享锁：`select * from table_name where ... lock in share mode;`
- 给select添加排它锁：`select * frome table_name where ... for update; `



## 表级锁、页面锁、行级锁

- 表级锁，开销小、加锁快、锁粒度大，不会出现死锁，并发度比较低
- 行级锁，开销大、加锁慢、锁粒度小，会出现死锁，并发度高
- 页面锁，开销和加锁时间界于表锁和行锁之间，会出现死锁，并发度一般

# InnoDB间隙锁

使用范围条件检索数据，而不是使用相等条件检索数据，如果要请求共享锁或排它锁时，InnoDB会给符合条件的并且是已存在的数据记录索引项加锁。而键值在条件范围内但不存在的记录叫做间隙GAP，InnoDB会对这个间隙加锁，这种就是间隙锁（Next-Key锁）

使用间隙锁目的：

- 防止幻读
- 满足恢复和复制需要

MySQL通过binlog记录更新的sql语句，slave机器基于binlog进行同步，重新执行binlog中的sql语句，binlog中的sql语句是按照事务提交的先后顺序记录，恢复的时候也是按照这个顺序进行，在一个事务未提交之前，其他并发事务不能插入满足其锁定条件的任何记录，不允许出现幻读。

# 锁算法

- Record Lock，记录锁，锁定一个行记录
- Gap Lock，间隙锁，锁定一个区间
- Next-key Lock，记录锁+间隙锁，锁定区间和行记录

InnoDB的Repeatable Read隔离级别下的等值查询锁算法示例：

- 主键索引（聚簇索引），对主键索引记录加记录锁
- 唯一索引，对辅助索引加记录锁，对主键索引也加记录锁
- 普通索引，对相关的辅助索引加Next-key Lock，对对应的主键索引加记录锁
- 不使用索引，对全表加Next-key Lock

# 参考

- [https://zhuanlan.zhihu.com/p/29150809](https://zhuanlan.zhihu.com/p/29150809)
- [https://juejin.im/post/5b85124f5188253010326360](https://juejin.im/post/5b85124f5188253010326360)
- [https://segmentfault.com/a/1190000014133576](https://segmentfault.com/a/1190000014133576)