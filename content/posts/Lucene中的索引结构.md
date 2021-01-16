---
title: Lucene中的索引结构
date: 2018-06-18 14:08:31
categories: 
- Lucene
tags:
- Lucene
---

Lucene中的索引结构学习。

<!--more-->

Lucene支持两种索引结构：多文件索引结构和复合索引结构。多文件索引结构使用多个文件来表示索引，复合索引结构使用一个特殊的文件来表示。

默认是用的是复合索引结构，可通过设置`useCompoundFile=false`来启用多文件索引结构。

# 多文件索引结构

通过设置`useCompoundFile=false`来启用多文件索引结构

```bash
_0.dii
_0.dim
_0.fdt
_0.fdx
_0.fnm
_0.nvd
_0.nvm
_0.si
_0.tvd
_0.tvx
_0_Lucene50_0.doc
_0_Lucene50_0.pay
_0_Lucene50_0.pos
_0_Lucene50_0.tim
_0_Lucene50_0.tip
segments_1
write.lock

```

- segments_1，段文件，保存了现有索引段的名称以及相关信息，每当IndexWriter向索引提交修改之前，段文件的值会增加1。在访问索引目录中任何文件前，Lucene都会查找该段文件，以确认要打开和读入的索引文件
- write.lock，锁文件，可防止多个IndexWriter同时操作同一个索引文件
- .si，Segment Info，保存Segment的元数据信息
- .fnm，Fields，保存fields的相关信息，包括：是否已被索引、是否允许使用项向量、是否存储norms、是否包含有效负载
- .fdx，Field Index，保存指向Field Data的指针
- .fdt，Field Data，文档中存储的Field的值
- .tim，Term Directory，存储Term信息
- .tip，Term Index，到Term Directory的索引
- .doc，Frequencies，由包含每个Term以及频率的docs列表组成
- .pos，Positions，存储出现在索引中的Term位置信息
- .pay，Payloads，存储额外的per-position元数据信息，例如字符偏移和用户payloads
- .nvd，Norms，保存索引字段加权数据
- .nvm，Norms，保存索引字段加权因子的元数据
- .tvx，Term Vector Index，将偏移存储到文档数据文件中
- .tvd，Term Vector Documents，包含有Term Vectors的每个文档信息
- .dii，.dm，Point Values，保留索引点

## 索引段

Lucene索引由一个或多个Segment组成，每个Segment由多个索引文件组成，属于同一个Segment的索引文件具有相同的前缀名

新添加的文档会添加到新创建的索引Segment中，多个Segment周期性的进行合并，该过程提高了效率，最大限度的减少了对物理索引文件的修改。

## .fdx和.fdt

一个文档中所有Field数据都存在.fdt中，可设置Field.Store.NO不存储这个Field的原始数据。

# 复合索引结构

通过设置`useCompoundFile=true`来启用多文件索引结构，默认启用，不需要设置。

```bash
_0.cfe
_0.cfs
_0.si
segments_1
write.lock

```

- .cfs、.cfe，Compound File，复合索引文件，所有的索引信息都存在这些文件中

复合索引减少了索引文件数量，把各个独立的索引文件封装在了cfg文件中

其他的文件：

- .dvd，Per-Document Values，保存索引文档评分数据
- .dvm，Per-Document Values，保存索引文档评分因子数据
- .tvf，Term Vector Fields，字段级别有关Term Vectors的信息
- .liv，Live Documents，哪些文件是有效的文件

# 参考

- Lucene实战

