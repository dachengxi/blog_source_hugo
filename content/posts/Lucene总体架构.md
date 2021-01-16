---
title: Lucene总体架构
date: 2018-06-16 16:01:30
categories: 
- Elasticsearch
tags:
- Elasticsearch
---

Lucene基础学习。

<!--more-->

# 基础概念

- 文档document，索引和搜索的主要数据载体，包含一个或多个字段，存放写入索引的数据
- 字段field，文档的一个片段，包含字段名称和字段内容
- 词项term，搜索时的一个单位，代表文本中的一个词
- 词条token，词项在field文本中的一次出现包括词项的文本、开始和结束的偏移以及词条类型

每个索引由多个段segment组成，每个段写入一次但可查询多次。索引期间一个段创建以后不可再修改，文档被删除后删除信息单独保存在一个文件中，段本身没有被修改。

多个段将会在段合并阶段被合并在一起，或者强制执行段合并，或者由Lucene内在机制触发段合并。合并后段数量变少，段大小可能变大。

段合并非常耗I/O，合并期间会清理掉被删除的文档。



# 文档

文档是Lucene索引基本单位，比文档更小的单位是字段，字段由三个部分组成：

- name
- type
- value

# Lucene字段类型

- TextField，字段内容会被索引并词条化，但不保存词向量
- StringField，只会对该字段内容索引，不词条化，也不保存词向量，字符串值会被索引为一个单独的词项
- IntPoint，适合索引值为int类型的字段
- LongPoint，long类型
- FloatPoint，float类型
- DoublePoint，double类型
- SortedDocValuesField，存储多值域的DocValues字段，适合索引字段值为文本并且需要按值进行分组、聚合等操作字段
- NumbericDocValuesField，存储单个数值类型的DocValues字段
- SortedNumbericDocValuesField，存储数值类型的有序数组列表的DocValues字段
- StoredField，适合索引只需要保存字段值不进行其他操作的字段

# 参考

- 从Lucene到Elasticsearch全文检索实战
- 深入理解ElasticSearch

