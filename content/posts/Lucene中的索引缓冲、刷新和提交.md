---
title: Lucene中的索引缓冲、刷新和提交
date: 2018-06-17 19:43:02
categories: 
- Lucene
tags:
- Lucene
---

Lucene中的索引缓冲、刷新和提交学习。

<!--more-->

# 缓冲和刷新

创建和删除文档操作时，会先被缓存之内存，而不是立即写盘，这些操作会以 衅端的形式周期性的写入索引的Directory，IndexWriter刷盘操作时机如下：

- 当缓存占用空间超过设置的阈值，setRAMBufferSizeMB来设置
- 在指定文档号对应的文档被添加进索引之后，通过调用setMaxBufferedDocs来完成刷新操作
- 在删除和查询语句等操作占用内存总量超过预设值时，可通过调用setMaxBufferedDeleteTerm方法来触发刷新操作

默认情况下IndexWriter在RAM用量为16M的时候启动刷新操作。

发生刷新操作时，Writer会在Directory目录创建新的段和被删除文件，这些文件对于新打开的IndexReader不可见也不可用，直到Writer提交更改以及重新打开Reader后。

- 刷新操作是用来释放缓存的更改
- 提交操作是用来让所有更改在索引中保持可见。

IndexReader所看到的一直是索引的起始状态，也就是IndexWriter被打开时的状态，直到IndexWriter提交更改为止。

# 索引提交

IndexWriter的commit方法调用，都会创建一个新的索引提交，新打开或重启的IndexReader和IndexSearcher只能看到上次提交后的索引状态，IndexWriter在两次提交之间完成的更改对Reader来说不可见。而近实时搜索功能能够在不用首次向磁盘提交更改的情况下对IndexWriter所做的更改进行搜索。

如果需要取消所有更改，可在上一次索引提交更改后，调用rollback方法删除当前IndexWriter包含的所有更改。

IndexWriter的提交步骤：

- 刷新所有缓存的文档和文档删除操作
- 对所有新创建的文件进行同步，包括刷新的文件，还包括上一次调用commit方法或从打开IndexWriter后已完成的段合并操作所生成的所有文件。IndexWriter调用Directory.sync方法实现，该方法会在所有挂起的写操作都写到磁盘后才返回
- 写入和同步下一个segment_X文件，一旦完成，IndexReader会立即看到上一次提交后发生的所有变化。
- 通过调用IndexDeletionPolicy删除旧的提交。

# 两阶段提交

对于需要提交包括Lucene索引和其他外部资源等事务的应用程序，Lucene提供了prepareCommit方法，该方法完成了上面步骤的前两步或前三部，但不能使新的segment_X文件对Reader可见，调用prepareCommit后还需要调用rollback或commit方法。

# 索引删除策略

IndexDeletionPolicy负责通知IndexWriter何时能够安全删除旧的提交，默认策略KeepOnlyLastCommitDeletionPolicy，会在每次创建完新的提交后删除先前的提交。

# 参考

- Lucene实战

