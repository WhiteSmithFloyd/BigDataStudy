
# Hbase Installation

### Configuration
1. 修改conf目录下的文件 hbase-env.sh 
+ export java_home=/
+ 不使用hbase自带的zk，则`HBASE_MANAGES_ZK`=`false`

2. 修改hbase-site.xml
``` xml
<!-- 指定habse所有表数据存储的位置，也可以指定为本地，类似hive的数据仓库 -->
<property>
	<name>hbase.rootdir</name>
	<value>hdfs://h1:8020/hbase</value>
</property>

<!-- 指定hbase的模式，是否使用分布式集群模式. false: 单机模式 true: 分布式(伪分布式或者完全分布式都是分布式) -->
<property>
	<name>hbase.cluster.distributed</name>
	<value>true</value>
</property>

<!-- 指定zk的实例化地址，zk是hbase的访问入口，客户端访问时都要经过zk -->
<property>
	<name>hbase.zookeeper.quorum</name>
	<value>http://h1:2181</value>
</property>

```

3. region server
编辑`regionservers`文件
```
h1 	# [hostname] 标记自己的主机名 类似于hadoop的slave节点
```

### Using Right lib 
1. 替换jar包:  hadoop 15个、 zk 1个
统一版本，以安装hadoop的版本为准


### Launch hbase
1. 先启动 hadoop 和 zk
2. 在启动hbase相关的两个进程
```shell
bin/start-hbase.sh
bin/stop-hbase.sh
# 或者
bin/hbase-daemon.sh start master 
bin/hbase-daemon.sh start regionserver 

```


### Testing
webUI 端口 `60010`
