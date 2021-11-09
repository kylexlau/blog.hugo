+++
title = "Cassandra TTL机制"
date = "2020-11-24"
+++

## 简介
研究Cassandra TTL 和墓碑的关系，TTL 清理数据的具体实现方式。

## TTL 是什么
TTL，Time To Live，生存时间，是 Cassandra 数据库提供的一个数据自动清理机制，可对
数据库中的数据设置TTL，超过生存时间后数据过期，数据库会自动进行删除。

- 表级别 TTL（表属性：`default_time_to_live`），插入表的每行数据均会使用这个默认 TTL。
- 行级别 TTL，插入每行数据时指定这行数据的 TTL。
- 列级别 TTL，update 某列数据时可指定 TTL。

Tombstone，墓碑，是 Cassandra 数据库将删除转化为插入操作的一个机制，本质就是一条
新插入的标记数据（主键+[列]+删除时间）。但对 TTL 来说，墓碑不是新插入的，而是修
改原有的数据，将其转换成墓碑。

什么情况会产生墓碑：

- CQL DELETE 语句
- 带TTL的数据过期，合并sstable 文件时数据会变成墓碑（将删除实际值）
- 带 null 值的 INSERT 或 UPDATE 语句
- UPDATE 更新集合类型的列
- 内部操作，如使用物化视图

## 怎么清理TTL过期数据
清理数据流程：

- 开始compact sstable文件
- 文件中如果存在 TTL过期数据，就将其变成 tombstone（其实这一步 sstable 文件就会
  大大减小，实际数据已经删除。tombstone 只是个标记，标记数据何时删除的。）
- 文件中如果有 gcable tombstone（判断能否 gc，就看 tombstone 的
  localDetetionTime+gc grace 是否小于当前时间），将 tombstone 清理掉

什么时候检查是否需要做compaction？

- 节点新增加了 sstable 时（如memtable 刷盘或repair 从别的节点复制了新的 sstable）
- 自动 compaction 被关闭又重新开始(`nodetool enableautocompaction`)
- 每5分钟检查一次

什么时候会真正compaction？

- 默认使用STCS，基于 sstable 文件大小做 compaction
- STCS 策略文件默认配置，有四个差不多大小的 sstable 生成后会触发 minor
  compaction。默认在0.5和1.5倍的范围可以放在一个桶做 compaction，最小文件大小是
  50M
  - `DEFAULT_MIN_SSTABLE_SIZE = 50L * 1024L * 1024L`
  - `DEFAULT_BUCKET_LOW = 0.5`
  - `DEFAULT_BUCKET_HIGH = 1.5`
  - `min_threshold = 4`(一个桶里至少需要有四个sstable后才会开始compact)
- tombstone 相关默认配置，单 sstable墓碑比例超过20%，会触发单个 sstable 的
  compaction（其实就是清理ttl 过期数据和墓碑）。
  - `DEFAULT_TOMBSTONE_THRESHOLD = 0.2f`(如果墓碑（包括已过期但还没转换成墓碑的
    数据）比例超过20%，会触发单sstable的compaction)
  - `DEFAULT_TOMBSTONE_COMPACTION_INTERVAL = 86400`(一天内不会对一个文件做重复compaciton)
- 手动触发 major compaction，也就是将所有 sstable 合并成一个文件。（`nodetool compact <keyspace> <table>`)
- 手动触发 gc（`nodetool garbagecollect <keyspace> <table>`）

## 参考代码
- [AbstractCell.java](https://github.com/apache/cassandra/blob/7a7eece9578312a2f9d77de6e0755a3c3c542e99/src/java/org/apache/cassandra/db/rows/AbstractCell.java)

为什么要将过期数据先转换成 tombstone，而不是等 gc 时间到之后跟 tomestone 一样的逻辑一并清理掉？就是在 gc 时间还没到之前先转换成 tombstone，转换过程中把实际数据（column value）删除了，能节省空间。
