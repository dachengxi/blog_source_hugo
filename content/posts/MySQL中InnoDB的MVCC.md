---
title: MySQL中InnoDB的MVCC
date: 2018-04-16 22:59:10
categories: MySQL
tags: 
- MySQL


---

MySQL的InnoDB引擎的MVCC学习。

<!--more-->

# 当前读（锁定读）

当前读就是加锁读，读取的是记录的最新版本，MySQL会加锁保证其他并发事务不能修改当前记录，直到当前事务释放锁。

当前读的操作主要包括：

- 显式加锁的读操作：`select ... lock in share mode;`和`select ... for update`
- 写操作insert、update、delete

update语句执行时，MySQL server会根据where条件读取第一条满足的记录，然后InnoDB会将第一条记录返回并加锁，MySQL server收到这条加锁记录后，会再发起一个update请求更新这条记录，这条记录操作完成后会继续读取下一条记录直到完成所有记录。update操作内部包含了当前读。

delete操作和update操作一样。

insert操作可能会触发Unique Key的冲突检查，也会有当前读操作。

# 快照读（一致性读）

快照读是不加锁读，读取记录的快照版本，而不是记录的最新版本，使用MVCC实现。

InnoDB默认的事务隔离级别Repeatable Read，读操作是快照读，如果显式加了锁的读操作不是快照读，是当前读。保证了事务执行过程中只有第一次读之前提交的修改和自己的修改可见，其他事务的修改当前事务均不可见。

# MVCC

MVCC：多版本并发控制，用来解决读写冲突的无锁并发控制，读不加任何锁，读写不冲突，多操作多于写操作的应用可极大增加系统并发性能。只在Repeatable Read和Read Committed两个隔离级别下工作。

InnoDB默认隔离级别Repeatable Read的不加锁的读操作是快照读，使用MVCC实现。

InnoDB的MVCC是在每行记录后面添加两个隐藏列来实现：一个保存行的创建版本号；一个保存行的删除版本号。事务开始时候的系统版本号会作为事务的版本号，用来和查询到的每行记录的版本号比较。没开始一个新的事务，系统版本号会递增。

Repeatable Read隔离级别下的MVCC操作：

- select操作：InnoDB只查找版本早于当前事务版本的数据行，可以确保事务读取的行要么是在事务开始前已存在的，要么是事务自身插入或修改过的；行的删除版本要么没有定义，要么大于当前事务版本号，可以确保事务读取到的行在事务开始之前未被删除
- insert操作，InnoDB为新插入的每一行保存当前系统版本号作为行版本号
- delete操作，InnoDB为删除的每一行保存当前系统版本号为行删除标识
- update操作，InnoDB会插入一行新纪录，保存当前系统版本号为新行版本号，同时将当前系统版本号作为原来行的行删除标识

MVCC只在Read Committed和Repeatable Read两个隔离级别下工作，Read Uncommitted读取的总是最新的数据行，Serializable会对所有读取行加共享锁。

# MySQL的MVCC

InnoDB默认隔离级别是Repeatable Read，通过MVCC解决了不可重复读。使用间隙锁解决幻读。

不可重复读发生是因为读和写没有互斥，如果使用读写互斥，又会导致并发度降低，MVCC可解决读写互不阻塞和不可重复读问题。

| COLUMN1 | COLUMN2 | ...... | DATA_TRX_ID | DATA_ROLL_PTR | DB_ROW_ID      |
| ------- | ------- | ------ | ----------- | ------------- | -------------- |
| 列1     | 列2     | ...... | 事务ID      | 回滚段指针    | 隐藏单调自增ID |

InnoDB每一行都会有两个隐藏列：

- DATA_TRX_ID，记录最近更新这条记录的事务ID，6字节大小
- DATA_ROLL_PTR，指向该行回滚段的指针，7字节大小。InnoDB可通过这个指针找到之前版本的数据。该行记录上所有的旧版本都在undo日志中通过链表形式组织
- DB_ROW_ID，隐藏的单调自增ID，6字节大小。如果表没有指定主键，InnoDB会自动生成一个隐藏的自增主键，就是这个列。
- 每条记录的头信息还有一个专门的位表示当前记录是否已经被删除

更新行的过程：

- 对当前行加排它锁
- 把当前行的值拷贝到undo log中
- 修改当前行的值，更新当前行DATA_TRX_ID为当前事务ID，将DATA_ROLL_PTR指向刚才拷贝到undo log中的记录
- 记录redo log，包括undo log中的修改

# 参考

- [https://segmentfault.com/a/1190000014133576](https://segmentfault.com/a/1190000014133576)
- [https://zhuanlan.zhihu.com/p/91208953](https://zhuanlan.zhihu.com/p/91208953)
- [https://zhuanlan.zhihu.com/p/64576887](https://zhuanlan.zhihu.com/p/64576887)