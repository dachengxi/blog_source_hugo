---
title: Lucene中的基础核心类
date: 2018-06-17 11:33:26
categories: 
- Lucene
tags:
- Lucene
---

Lucene索引创建和搜索索引的核心类学习。

<!--more-->

# 索引过程中的核心类

## IndexWriter

IndexWriter负责创建新索引或者打开已有索引，向索引中添加、删除、更新被索引文档的信息。IndexWriter需要开辟一定空间来存储索引，由Directory完成。

## Directory

Directory描述了索引的存放位置，子类负责具体指定索引的存储路径。

## Analyzer

Analyzer负责从被索引文本中提取词汇单元，也就是分词。

## Document

Document文档，代表一些域Field的集合。包含多个Field对象的容器。

## Field

索引中的每个文档都包含一个或者多个不同命名的域，这些域包含在Field中，每个域都有域名和对应值

# 搜索过程中的核心类

## IndexSearcher

IndexSearcher用来搜索由IndexWriter创建的索引。IndexSearcher可看做一个以只读方式打开索引的类，需要利用Directory来找到索引位置。

## Term

Term对象是搜索功能的基本单元，包含域名和值

## Query

Query查询，有很多子类：TermQuery、BooleanQuery、PhraseQuery、PrefixQuery、PhrasePrefixQuery、TermRangeQuery、NumbericRangeQuery、FilteredQuery、SpanQuery

## TermQuery

词项查询，用来匹配指定域中包含特定项的文档。

## TopDocs

是一个简单的指针容器，指针一般指向前N个排名的搜索结果。

## ScoreDoc

提供对TopDocs中每条搜索结果的访问接口

## QueryParser

将用户输入的查询表达式处理成具体的Query对象

# 参考

- Lucene实战

