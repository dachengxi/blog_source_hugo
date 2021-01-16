---
title: MySQL的一致性读、快照读、锁定读、当前读
date: 2018-04-18 23:34:32
categories: MySQL
tags: 
- MySQL


---

MySQL的一致性读、快照读、锁定读、当前读学习。

<!--more-->

# 一致性读（快照读）

一致性读，也称为快照读，读取的是快照版本。普通的select是快照读。在事务中select的时候会生成一个快照，不同隔离级别生成快照的时机不一样：

- Read Committed隔离级别，在一个事务中每次读取都会重新生成一个快照，每次快照都是最新的，所以当前事务中每次select操作都可以看到其他已提交事务所做更改
- Repeatable Read隔离级别，在一个事务中的第一次select执行的时候生成快照，只有在当前事务中对数据的修改才会更新快照。只有第一次select之前其他已提交事务所做更改可以看到，第一次select之后其他事务提交的更改当前事务是看不到的

一致性读，主要基于MVCC实现，多版本控制核心是数据快照，InnoDB通过undo log存储数据快照。

使用MVCC优势是不加锁，并发度高，但是读取的数据不是实时数据。

# 锁定读（当前读）

锁定读，也叫当前读，读取的是最新版本，update、delete、insert、select...lock in share mode、select...for update都是锁定读。

通过加record lock和gap lock间隙锁来实现，也就是next-key lock。使用next-key lock优势是获取实时数据，但是需要加锁。



# 参考

- [https://blog.csdn.net/z69183787/article/details/81709743](https://blog.csdn.net/z69183787/article/details/81709743)