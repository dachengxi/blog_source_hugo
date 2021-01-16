---
title: Lucene中的索引过程和基本操作
date: 2018-06-17 13:25:05
categories: 
- Lucene
tags:
- Lucene
---

Lucene索引过程学习。

<!--more-->

# 索引过程

```
提取数据 -> 分析文本 -> 添加文档到索引
```

## 提取文本

建立索引数据时，需要先从数据中提取纯文本格式信息。

提取完文档后还需要建立Document文档。

## 分析文档

建立Document和Field后，会对文本进行分析，将文本分割成词项，这一步就是进行分词等操作。比如需要将词项转换为小写，去掉一些stop words等等。

## 向索引添加文档

分析完数据后，就可以将分析结果写入索引文件。存储的时候是使用倒排索引的数据结构进行存储。进行关键字快速查找时，这种数据结构能有效利用磁盘空间。

Lucene索引都包含一个或多个段segment，每个段都是一个独立索引，包含整个文档索引的一个子集。

每个段都包含多个文件，各个独立的文件共同组成了索引的不同组成部分（Term向量、存储的Field、倒排索引等等）。

如果使用了混合文件格式，上面说的索引文件都会被压缩成一个单一的文件，这种方式能够在搜索期间减少打开文件数量。

还有个特殊文件叫段文件，`segments_x x=1,2,3...`，该文件指向所有激活的segment，Lucene会首先打开该文件，然后打开它所指向的其他文件，其中的x（x=1,2,3...)被称为the generation，是一个整数，Lucene每次向索引提交更改的时候，都会将这个数字加1

IndexWriter会周期性的选择一些segment，将它们合并到一个新segment中，然后删除老的segment

# 索引基本操作

## 向索引添加文档

- `addDocument(Document)`，添加文档，其中使用的Analyzer在创建IndexWriter时，由IndexWriterConfig中指定。

## 删除索引中的文档

- `deleteDocuments(Term...)`，删除包含指定词项的文档
- `deleteDocuments(Query...)`，删除匹配查询语句的文档

## 更新索引中的文档

Lucene只能删除整个旧文档，然后向索引中添加新文档。这就要求新文档必须包含旧文档中所有Field，包括未发生变化的Field。

- `updateDocument(Term, Document)`，首先删除包含Term的文档，然后添加新文档

# 参考

- Lucene实战

