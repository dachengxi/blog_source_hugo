---
title: Spring事务简介
date: 2016-06-13 17:16:37
categories: Spring
tags:
- Spring
---

## 事务的传播行为
Spring事务有7种传播行为:

1. PROPAGATION_REQUIRED 支持当前事务，如果当前没有事务，就新建一个事务。这是最常见的选择。
2. PROPAGATION_SUPPORTS 支持当前事务，如果当前没有事务，就以非事务方式执行。
3. PROPAGATION_MANDATORY 支持当前事务，如果当前没有事务，就抛出异常。
<!-- more -->
4. PROPAGATION_REQUIRES_NEW 新建事务，如果当前存在事务，把当前事务挂起。
5. PROPAGATION_NOT_SUPPORTED 以非事务方式执行操作，如果当前存在事务，就把当前事务挂起。 
6. PROPAGATION_NEVER 以非事务方式执行，如果当前存在事务，则抛出异常。
7. PROPAGATION_NESTED 如果一个活动的事务存在，则运行在一个嵌套的事务中. 如果没有活动事务, 则按PROPAGATION_REQUIRED 属性执行 

## 事务隔离级别
Spring事务有5种隔离级别

1. ISOLATION_DEFAULT 这是一个PlatfromTransactionManager默认的隔离级别，使用数据库默认的事务隔离级别.
2. ISOLATION_READ_UNCOMMITTED 这是事务最低的隔离级别，允许读取尚未提交的更改。可能导致脏读、幻影读或不可重复读。
3. ISOLATION_READ_COMMITTED 允许从已经提交的并发事务读取。可防止脏读，但幻影读和不可重复读仍可能会发生。
4. ISOLATION_REPEATABLE_READ 对相同字段的多次读取的结果是一致的，除非数据被当前事务本身改变。可防止脏读和不可重复读，但幻影读仍可能发生。
5. ISOLATION_SERIALIZABLE 这是花费最高代价但是最可靠的事务隔离级别。事务被处理为顺序执行。除了防止脏读，不可重复读外，还避免了幻像读。

## 只读

## 事务超时

## 回滚规则
Spring默认情况下会对RunTimeException进行事务回滚。这个异常是unchecked,如果遇到checked意外就不回滚。

改变默认规则：

1. 让checked例外也回滚：在整个方法前加上 @Transactional(rollbackFor=Exception.class)
2. 让unchecked例外不回滚： @Transactional(notRollbackFor=RunTimeException.class)
3. 不需要事务管理的(只查询的)方法：@Transactional(propagation=Propagation.NOT_SUPPORTED)
4. 如果不添加rollbackFor等属性，Spring碰到Unchecked Exceptions都会回滚，不仅是RuntimeException，也包括Error。

注意:如果异常被try｛｝catch｛｝了，事务就不回滚了，如果想让事务回滚必须再往外抛try｛｝catch｛throw Exception｝。


## 参考
[http://fhjxp.iteye.com/blog/124978](http://fhjxp.iteye.com/blog/124978)

[http://liubingwwww.blog.163.com/blog/static/3048510720091842335402/](http://liubingwwww.blog.163.com/blog/static/3048510720091842335402/)

[http://www.cnblogs.com/zhishan/p/3195219.html](http://www.cnblogs.com/zhishan/p/3195219.html)

[http://www.cnblogs.com/0201zcr/p/4678649.html](http://www.cnblogs.com/0201zcr/p/4678649.html)