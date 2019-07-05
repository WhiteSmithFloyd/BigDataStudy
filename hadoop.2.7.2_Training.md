# Hadoop 2.7.2

# hadoop 伪集群
## 配置 
hadoop-env.sh
core-site.xml
hdfs-site.xml

## 启动集群
### 格式化 namenode
bin/hdfs namenode -format 
### 启动 namenode
sbin/hadoop-daemon.sh start namenode  
### 启动 datanode
sbin/hadoop-daemon.sh start datanode  

## 查看集群
查看是否启动成功
jps
查看log日志
web端查看HDFS文件系统
http://localhost:50070  

##  HDFS的操作
```shell
hadoop fs -mkdir -p /home/hadoop/training
hadoop fs -ls -R /    等价  hadoop fs -lsr /

# hadoop fs -put source target 
hadoop fs -put /home/test_area/hadoop/wordCount  /home/hadoop/training/wordCount

# 在HDFS上运行 wordCount
hadoop jar share/hadoop/mapreduce/hadoop-mapreduce-examples-2.7.2.jar wordcount /home/hadoop/training/wordCount/input  /home/hadoop/training/wordCount/output
 
hadoop fs -cat /home/hadoop/training/wordCount/output/

hadoop fs -rmr /home/hadoop/training/wordCount/output/
```


# yarn 上运行MapReduce程序
## 配置集群
+ yarn-env.sh for JAVA_HOME
+ yarn-site.xml
```xml
<configuration>
	<!-- reducer 获取数据的方式 --> 
    <property>
        <name>yarn.nodemanager.aux-services</name>
        <value>mapreduce_shuffle</value>
    </property>
	<!-- 指定YARN的resourcemanager的地址 --> 
	<property>
        <name>yarn.resourcemanager.hostname</name>
        <value>h1</value>
    </property>
</configuration>
```
+ mapred-env.sh  for JAVA_HOME
+ 重命名 mapred-site.xml.template 为 mapred-site.xml
```xml
<configuration>
    <property>
        <name>mapreduce.framework.name</name>
        <value>yarn</value>
    </property>
</configuration>
```
## 启动集群
+ 先启动集群 namenode 和 datanode
+ 启动 resourcemanager
sbin/yarn-daemon.sh start resourcemanager 
+ 启动 nodemanager
sbin/yarn-daemon.sh start nodemanager 

## 集群操作
+ yarn的浏览器上查看
http://h1:8088/cluster 
+ 删除文件系统上的output文件
+ 执行MapReduce程序
+ 查看运行结果
