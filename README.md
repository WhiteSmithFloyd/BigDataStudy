# BigDataStudy

# hadoop 伪集群
1. 配置 
hadoop-env.sh
core-site.xml
hdfs-site.xml
2. 启动
格式化 namenode
bin/hdfs namenode -format 
启动 namenode
sbin/hadoop-daemon.sh start namenode  
启动 datanode
sbin/hadoop-daemon.sh start datanode  
3. 查看
查看集群
查看是否启动成功
jps
查看log日志
web端查看HDFS文件系统
localhost:50070  

# HDFS的操作
hadoop fs -mkdir -p /home/hadoop/training
hadoop fs -ls -R /    等价  hadoop fs -lsr /

# hadoop fs -put source target 
hadoop fs -put /home/test_area/hadoop/wordCount  /home/hadoop/training/wordCount

# 在HDFS上运行 wordCount
hadoop jar share/hadoop/mapreduce/hadoop-mapreduce-examples-2.7.2.jar wordcount /home/hadoop/training/wordCount/input  /home/hadoop/training/wordCount/output

hadoop fs -cat /home/hadoop/training/wordCount/output/

hadoop fs -rmr /home/hadoop/training/wordCount/output/
