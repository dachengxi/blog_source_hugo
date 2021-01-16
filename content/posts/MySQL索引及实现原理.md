---
title: MySQL索引及实现原理
date: 2018-04-15 14:42:41
categories: MySQL
tags: 
- MySQL

---

MySQL索引、数据结构、算法等学习。

<!--more-->

只看MyISAM和InnoDB两个存储引擎。

# MySQL索引

## MyISAM引擎

MyISAM使用B+树作为索引结构，叶节点存放的是数据记录地址。MyISAM的索引文件仅存放数据记录的地址，主索引和辅助索引在结构上没有任何区别，主索引要求key是唯一的，辅助索引key可以重复。

MyISAM的索引方式被称为：非聚集索引。

MyISAM中索引检索算法为：首先按照B+树算法搜索索引，如果指定的key存在，取出其data中的值，然后以data中的值为地址读取对应地址存放的数据记录。

## InnoDB引擎

InnoDB使用B+树作为索引结构，叶节点保存了完整的数据记录，InnoDB表数据文件本身就是主索引。

InnoDB的索引方式被称为：聚集索引。

InnoDB数据文件本身按主键聚集，所以InnoDB要求表必须具有主键，如果没有显式指定，则MySQL会自动选择一个可以唯一标识数据记录的列作为主键，如果不存在这种列，则MySQL自动为InnoDB表生成一个隐含字段作为主键，字段长度6字节，长整型。

InnoDB的辅助索引叶节点上记录的是主键。InnoDB使用主键搜索很高效，但是使用辅助索引搜索会先检索获得主键，再根据主键到主索引中检索才能获得数据记录。

# 为什么使用B+树

B-树的内结点存储data，而B+树的内结点不存储data只存储key。B+树更适合实现外存储索引结构。

一般数据库系统或文件系统中使用的B+树都进行了优化，增加了顺序访问指针，提高区间访问性能。

索引文件一般以文件的形式存储在外部磁盘上，使用索引的时候需要磁盘IO，磁盘操作需要寻道和旋转，速度很慢，相当耗时。

局部性原理：当一个数据被使用到时，附近的数据也通常会马上被使用到。

磁盘预读：当从磁盘读取数据时，除了读取需要的数据，还会向后预读一定长度的数据放入内存。

索引查询效率高的指标就是IO次数少，采用B+树，可以使用很低的高度来存储很多的数据，相比红黑树这类适合内存的树，B+树更适合做磁盘索引数据结构。

# 参考

- [https://blog.codinglabs.org/articles/theory-of-mysql-index.html](https://blog.codinglabs.org/articles/theory-of-mysql-index.html)
- [https://tech.meituan.com/2014/06/30/mysql-index.html](https://tech.meituan.com/2014/06/30/mysql-index.html)