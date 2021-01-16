---
title: MySQL的undo log和redo log
date: 2018-04-18 20:32:05
categories: MySQL
tags: 
- MySQL


---

MySQL的undo log和redo log学习。

<!--more-->

InnoDB引擎提供了两种事务日志：redo log和undo log。redo log用于保证事务的持久性，undo log是事务原子性和隔离性实现的基础。

# undo log

回滚日志保存了事务发生之前的数据的一个版本，用于回滚和提供一致性读（MVCC）。

在事务开始之前，当前版本行数据生成undo log，undo log也会产生redo log来保证undo log的可靠性；事务提交之后undo log不会立马删除，会放入待清理的链表，由purge线程判断是否有其他事务在使用undo段中表的上一个事务之前的版本信息，决定是否可以清理undo log。

undo log是逻辑格式的日志，执行undo仅仅是将数据从逻辑上恢复至事务之前的状态，而不是从物理页面上操作。undo log也会产生对应的redo log。

undo log是为了实现事务的原子性，undo log还可以用来实现MVCC。操作任何数据前线将数据备份到undo log，然后进行数据修改，如果出现错误或者执行了rollback，系统可以利用undo log中备份将数据恢复到事务开始之前的状态。

当事务提交的时候，InnoDB不会立即删除undo log，因为后续还可能会用到undo log。事务提交的时候会将该事物对应的undo log放入到删除列表，未来通过purge来删除，提交事务时还会判断undo log分配的页是否可以重用，如果可以重用，则分配给后面来的事务，避免为每个事务分配独立的undo log页面浪费存储空间和性能。

delete操作不会直接删除数据，而是将delete对象打上delete标记，标记为删除，最终删除操作是通过purge线程完成的

update操作的列如果是主键列，update会先删除该行，在插入一行目标行；如果update的不是主键列，在undo log中直接反向记录是如何update的。

# redo log

重做日志可以确保事务的持久性，防止在发生故障的时候，脏页没有刷盘。重启MySQL服务的时候可以根据redo log进行重做，从而达到事务持久性这一特性。

事务开始之后就会产生redo log，事务执行过程中就会逐步将redo log落盘。当事务对应的脏页写到磁盘后，redo log就没用了。

redo log是物理格式的日志，记录的是物理数据页的修改信息，而不是某些行修改成怎样，redo log是顺序写入redo log file的物理文件中去的。

redo log会先写到Innodb_log_buffer缓冲区，然后通过以下方式再将缓冲区日志刷新到磁盘上：

- Master Thread每秒执行刷新Innodb_log_buffer到redo log 文件中
- 每个事务提交时会将redo log刷新到redo log文件中
- 当重做日志缓存可用空间少于一半时，重做日志缓存被刷新到redo log文件中

redo log记录的是新数据的备份，事务提交之前只要将redo log持久化即可，不需要持久化数据。系统崩溃时虽然数据没有持久化，但是redo log已经持久化，可以根据redo log内容将所有数据恢复到最新状态。

redo log以块为单位进行存储，每块大小512字节。redo log缓冲区刷到redo log文件时，是追加写入的方式，也就是顺序写盘。

# 参考

- [https://www.cnblogs.com/wy123/p/8365234.html](https://www.cnblogs.com/wy123/p/8365234.html)
- [https://yq.aliyun.com/articles/592937](https://yq.aliyun.com/articles/592937)
- [https://www.cnblogs.com/kismetv/p/10331633.html](https://www.cnblogs.com/kismetv/p/10331633.html)
- [https://www.cnblogs.com/f-ck-need-u/p/9010872.html](https://www.cnblogs.com/f-ck-need-u/p/9010872.html)