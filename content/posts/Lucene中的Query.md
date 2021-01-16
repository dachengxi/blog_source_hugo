---
title: Lucene中的Query
date: 2018-06-18 11:25:37
categories: 
- Lucene
tags:
- Lucene
---

Lucene中的各种Query学习。

<!--more-->

Lucene的Query包含：TermQuery、TermRangeQuery、NumbericRangeQuery、PrefixQuery、BooleanQuery、PhraseQuery、WildcardQuery、FuzzyQuery、MatchAllDocsQuery

# TermQuery

通过项进行搜索，对索引中特定项进行搜索，Term是最小的索引片段，Term包含了一个Field Name和一个Field Value。

# TermRangeQuery

在指定的项范围内搜索，索引中各个Term对象会按照字典编排顺序进行排序。适用于文本范围查询。

# NumbericRangeQuery

在指定的数字范围内搜索，如果使用NumbericField来索引Field，就可使用NumbericRangeQuery在某个特定范围内搜索该Field。

# PrefixQuery

通过字符串前缀搜索

# BooleanQuery

组合查询，可将各种查询组合成复杂的查询方式，可进行逻辑AND、OR、NOT组合。

- BooleanClause.Occur.MUST，必须匹配该查询子句
- BooleanClause.Occur.SHOULD，该查询自己是可选的
- BooleanClause.Occur.MUST_NOT，不包含匹配该查询子句的文档

# PhraseQuery

通过短语搜索，会根据各个词项的位置信息定位某个距离范围内的词项对应的文档

# WildcardQuery

通配符查询：

- `*`代表0个或多个字母
- `?`代表0个或1个字母

# FuzzyQuery

搜索类似项，模糊查询，用于匹配与指定项相似的项。

# MatchAllDoscQuery

匹配所有文档

# 参考

- Lucene实战

