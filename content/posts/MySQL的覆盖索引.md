---
title: MySQL的覆盖索引
date: 2018-04-20 19:02:49
categories: MySQL
tags: 
- MySQL

---

MySQL的覆盖索引学习。

<!--more-->

如果一个索引包含所有需要查询的字段，称为覆盖索引。只需要扫描索引无需回查表就可以得到需要的数据。

优点：

- 索引项通常比记录小，只访问索引可以得到数据，无需再回表操作，减少IO次数，提高速度
- 索引都按值的大小顺序排序，相对于随机访问记录，需要的IO次数更少
- 大多数引擎能缓存索引，比如MyISAM只缓存索引
- InnoDB使用聚集索引组织数据，如果二级索引中包含查找所需数据，就不需要再聚集索引中再次查找了

覆盖索引必须要存储索引列的值，哈希索引和Full-Text索引不存储值，不能使用覆盖索引。

如果要使用覆盖索引，不可以使用select * ，而要使用指定的列。

不能使用的情况：

- 如果select选择的字段中有不在索引中的字段，则不能选择覆盖查询。
- where条件中如果有对索引进行like的操作，不能使用

# 参考

- [https://blog.csdn.net/jh993627471/article/details/79421363](https://blog.csdn.net/jh993627471/article/details/79421363)
- [https://www.cnblogs.com/chenpingzhao/p/4776981.html](https://www.cnblogs.com/chenpingzhao/p/4776981.html)