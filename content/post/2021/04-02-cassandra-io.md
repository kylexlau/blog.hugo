+++
title = "Cassandra IO吞吐量优化实践"
date = "2021-04-02"
+++

之前在某个集群已经碰到过同样的问题，业务查询是单行查询，系统IOPS不高，但IO吞吐量
先达到磁盘瓶颈，IO使用率100%，导致数据库整体性能上不去。此集群查询量增加后，尤其
是用户表查询迁移过来后，虽然业务查询是走分区键的单行查询，但是操作系统的IO吞吐量
同样先达到了瓶颈。IO使用率接近100%。

针对这个问题，首先尝试在系统IO能力上做了优化，将磁盘数量从两个分拆到四个，理论上
IO吞吐能力增加了1倍。但是这种优化治标不治本，随着查询量增长，很快IO使用率又接近
了瓶颈。

最近几天，对IO吞吐量大的问题进行了深入分析和测试，明确了原因。

查询官方文档，分析Cassandra一次读取操作的过程如下：
- 查memtable（最终合并memtable和sstable的查询结果）
- 查row cache（默认关闭）
- 查Bloom filter
- 查partition key cache
- 如果partition key cache有数据，直接去compression offset map
- 如果partition key cache没数据，再查partition summary（内存）和partition index（磁盘）
- 使用compression offset map定位数据在磁盘上的位置
- 从SSTable读出数据（大IO操作肯定是在这一步）

几个概念：
- memtable，内存结构。数据先写入memtable，定期刷盘变成sstable文件。
- row cache，row_cache_size_in_mb，默认为0。行缓存，默认关闭。
- Bloom filter，内存结构。查询布隆过滤确认SSTable是否存在某分区键的数据。如果返
  回false，则肯定不存在，返回true，也只是可能存在。每个SSTable会有一个对应的
  Bloom filter。
- key cache，key_cache_size_in_mb，key cache内存最大值。默认为空，如果
  `5%*heap`大于100MB，就设置为100MB，否则设置为`5%*heap`。Each key cache hit
  saves one seek and each row cache hit saves a minimum of two seeks.
- partition summary，内存结构。保存抽样的分区键所在partition index文件位移信息。
  抽样率由表的min/max_index_interval参数决定。
- partition index，磁盘文件。保存分区键和它们的offset。
- compression offset map，内存结构。保存compressed offsets 和 uncompressed
  position之间的map，通过它能找到某分区键的数据在磁盘上的具体位置。每TB数据需要
  1-3GB的的内存用于compression offset map。

Cassandra的表默认是启用压缩的，默认压缩参数如下：

    compression = {'chunk_length_in_kb': '64', 'class':'org.apache.cassandra.io.compress.LZ4Compressor'}

默认会使用LZ4压缩算法做分块压缩，默认是64K一个块。那么每次读取sstable的数据，最
少必须读取64K，然后解压整个块，才能从中找到某个分区。`chunk_length_in_kb`，这个参
数控制压缩块大小，块越大，压缩比越大。

从原理上分析，分块压缩这块肯定会导致单次IO较大，单行查询可能只需几B的数据返回，
但落到磁盘也至少是32K（64K压缩后）的数据读取，会导致磁盘吞吐量高。

由于Linux操作系统的页大小是4KB，一次IO最小是4KB。在测试环境修改压缩参数，将表的
块大小修改为4KB，并做一次合并操作，数据重新压缩写入新的sstable文件。对优化后的表
进行单行读取压测，发现效果显著。于是对线上数据库查询最多的表user_info_cs也做了优
化操作，第二天观察发现效果不大。

继续分析可能导致单次IO较大的原因。对操作系统相关参数检查，发现几块SSD磁盘预读大
小均配置为4096，也就是4M。

    cat /sys/class/block/vdc/queue/read_ahead_kb
    4096

为了提高I/O性能，Linux操作系统使用了readahead（预读）技术，该技术假定后续读取可
能需要文件中的某些数据，因此会将该文件中的更多内容读入内存，而不只是所请求的部分。
较高的 readahead 值可增加吞吐量，但是会占用更多内存和 IOPS。较低的 readahead 值
可增加 IOPS，但是会牺牲吞吐量。对于随机访问数据较多的场景，一般是建议设置较低的
readahead值；而顺序访问数据较多，需优化吞吐量的场景（如Hadoop作业），更适合设置
较大的readahead值。

了解了readahead的特点后，重点怀疑readahead值设置较大会导致IO吞吐量放大。线上数据
库调整了一个节点的配置，第二天发现调整过的节点IO吞吐量下降明显。在查询QPS没有明
显变化的情况下，IO吞吐量从前两天的500MB/s降到了13MB/s，IO使用率、IOPS、CPU使用率、
系统负载等指标均有不同程度下降。后续对其他节点做同步优化，将整个数据库集群负载降
低到一个非常安全的水平。

至此，此Cassandra集群IO吞吐量放大问题原因明确，主要是受readahead参数和表压缩参数
配置的影响。当前线上数据库这两个参数均使用默认值，数据库还是需要针对不同业务场景
做针对性的优化。
