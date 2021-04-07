---
title: MySQL的explain介绍
date: 2018-04-21 22:33:34
categories: MySQL
---

MySQL的explain介绍和使用。

<!--more-->

explain可以模拟优化器执行查询语句，分析查询语句。使用方式：explain + sql语句。

explain执行后返回的信息有：

- id
- select_type
- table
- type
- possible_keys
- key
- key_len
- ref
- rows
- Extra

# id

id表示查询的序列号，表示查询中执行的select子句的顺序或者是查询中操作表的顺序。

- 如果id相同，表示从上往下依次顺序执行
- 如果id不同，如果是子查询，则id序号会递增，越大表示优先级越高，会被先执行。

如果id既有相同，又有不同的，则相同的id依次从上往下顺序执行；不同的id值越大优先级越高，会先执行。

# select_type

select_type表示查询类型，用来区分普通查询、子查询、联合查询等，具体类型如下：

- SIMPLE，简单查询，不包含子查询或者联合查询（union）
- PRIMARY，如果不是一个简单查询，则最外面的查询被标记为PRIMARY
- SUBQUERY，表示是一个复杂查询中的子查询
- DERIVED，字面意思是衍生的，在from中包含的子查询会被标记为DERIVED，mysql会递归执行子查询，并把子查询结果放到临时表中
- UNION，如果第二个select出现在union之后，则这个select会被标记为UNION；如果union包含在from子句的子查询中，则外层的select会被标记为DERIVED。
- UNION RESULT，表示从union表获取结果的select

# table

table就是执行的表名字

# type

type表示查询使用了什么类型，具体的类型有：

- ALL
- index
- range
- ref
- eq_ref
- const
- system
- NULL

各种类型查询的性能从好到差依次是：

`system > const > eq_ref > ref > range > index > all`

## system

表中只有一行记录的时候，如果执行查询，查询的类型是system，可以认为是const的特殊情况，一般不会出现。

## const

const字面意思是常量，表示通过索引一次就能找到。在where中通过primary key或者unique索引进行匹配的时候，只能匹配到一行数据，此时类型是const。

## eq_ref

eq_ref是唯一性索引扫描，每个索引键在表中只能匹配到一条记录，比如primary key或者unique索引。

## ref

ref是非唯一性索引扫描，通过一个索引可以匹配到多行记录。

## range

range类型的查询是范围查询，比如where语句中的between，`<`，`>`，in等返回查询，会使用where条件中的一个索引来选择行。

范围查询比全表扫描性能好点，范围查询的开始和结束都会走到索引，不需要扫描全部索引。

## index

index是Full Index Scan，扫描全部索引，会遍历索引树。

## ALL

ALL是Full Table Scan，全表扫描，遍历全表进行扫描。

index只会遍历索引树，索引文件一般会比数据文件小，因此index扫描会更快，all全表扫描，从硬盘读取数据，速度更慢。

# possible_keys

possible_keys会展示可能会被应用到这张表中的索引，会存在多个。如果一个查询中的字段存在索引，则会被展示到possible_keys中，但是索引不一定会被真正使用到。

# key

key表示实际使用到的索引，可能会存在如下情况：

- NULL，表示没有使用索引，可能是没有建立索引或者是索引失效了
- 如果使用覆盖索引（如果一个索引包含所有需要查询的字段，称为覆盖索引。只需要扫描索引无需回查表就可以得到需要的数据），则该索引出现在key中，possible_keys中为NULL

# key_len

表示索引的长度，索引的长度是索引字段的定义的长度，不是实际字段中存储的数据的长度。索引长度越短越好。

# ref

表示哪一个索引列被使用到了，最好是一个const常量。

# rows

表示可能会读取的行数

# Extra

Extra中显示的是一些额外信息，这些信息不会显示在其他的列中。一般有如下几种：

- Using filesort，文件排序，说明mysql会对数据使用外部的索引排序，不是按照表内的索引顺序进行读取。
- Using temporary，使用临时表保存中间结果，mysql在对查询结果排序的时候使用临时表保存，order by和group by中会比较常见。
- Using index，表示select中使用到了覆盖索引，直接获取到了数据，无需再访问数据文件，效率很好。如果同时出现了Using where，表示索引被在where中执行了匹配查找。
- Using where，表示索引被使用在where中进行了匹配过滤。
- Using join buffer，使用join的时候的缓存
- impossible where，表示where子句中总是false，说明where没有意义
- select table optimized away，
- distinct，

# 参考

- [https://blog.csdn.net/jh993627471/article/details/79421363](https://blog.csdn.net/jh993627471/article/details/79421363)
- [https://www.cnblogs.com/chenpingzhao/p/4776981.html](https://www.cnblogs.com/chenpingzhao/p/4776981.html)