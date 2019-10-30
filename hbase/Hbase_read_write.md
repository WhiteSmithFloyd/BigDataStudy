### 存储单元格
单元：` { row key, column(family+label),  version } `



### Hbase的所有数据文件都存储在HDFS上，主要包括两种类型
+ HFile: HBase中的KV数据的存储格式，HFile是Hadoop的二进制格式文件。StoreFile是对HFile做了轻量级包装，进行数据的存储
+ HLog File: 预写日志，HBase中WAL(Write Ahead Log)的存储格式，物理上是Hadoop的Sequence File。**顺序写入** 提高写入速度。



#### 预写日志WAL
+ 用户每次写入一份数据到Hlog文件中，同时写入到memstore
+ 写入成功后才会通知客户端该操作成功
+ 每个regionserver只有一个Hlog文件
+ Hlog文件定期的刷新，删除旧的文件
+ 避免内存中丢失数据，可以在日志文件中恢复




![structure](https://github.com/WhiteSmithFloyd/ress/blob/master/imgs/hbase/hbase_1.png)


### HBase的读写流程 Flush Compation Split 
+ Flush
  Client写入 -> 存入MemStore，一直到MemStore满(Hlog) -> Flush成一个StoreFile存储在HDFS上

+ Compact
  StoreFile，直至增长到一定阈值 -> 触发Compact合并操作 -> 多个StoreFile合并成一个StoreFile
  Compact -》 minjor、major、compaction

  + minjor 把多个小的StoreFile合成较大的1个storefile 

  ​        **不会将打上删除标记和失效(TTL)版本的数据清除**

  + major 把几个较大的storefile合成一个storefile 

  ​        **将打上删除标记和失效版本的数据清除**

+ Split
  单个StoreFile大小超过一定阈值后，触发Split操作，把当前Region Split成2个Region
  HMaster分配到相应的HRegionServer上




```xml
<!-- 当一个HStore含有多于这个值的HStoreFiles（每一个memstore flush产生一个HStoreFile）的时候，会执行一个合并操作，把这个HStoreFiles写成一个 -->
<properties>
  <name>hbase.hstore.compactionThreshold</name>
  <value>3</value>
</properties>
```



### 读写流程

如图

![flow](<https://github.com/WhiteSmithFloyd/ress/blob/master/imgs/hbase/hbase_2.png>)












































































































































































