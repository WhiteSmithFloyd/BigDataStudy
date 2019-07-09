
# Hadoop 完全分布式部署
## 准备三台设备
三台设备，同一局域网，hostname统一命名，配置JAVA环境

## 三台设备配置SSH环境
### SSH无密码登录
- 在home目录下找到当前用户对应的文件夹（root用户的文件夹在/root下）
- 找到.ssh/目录
- 执行下面命令
```shell
# 生成秘钥对（根据known-host文件中的公钥记录生成秘钥对）
ssh-keygen -t rsa
# 发送公钥到目标服务器
ssh-copy-id host
# 测试是否可以免密登录
ssh host
```

### rsync同步数据
```shell
  rsync -rvl /somewhere  abc@host:/somewhere 
```

## 基于伪分布式部署完全分布式
### 集群部署规划
|   h1   |   h2   |   h3   |
| ---- | ---- | ---- |
|   HDFS   |      |      |
|   namenode   |      |   secondaryNameNode   |
|   datanode   |   datanode   |   datanode   |
|   YARN   |      |      |
|   NodeManager   |   ResourceManager   |   NodeManager   |


### 修改配置文件
#### core-site.xml
```xml
<configuration>
    <property>
        <name>fs.defaultFS</name>
        <value>hdfs://localhost:9000</value>
    </property>
    <!-- Optional -->
    <property>
        <name>hadoop.tmp.dir</name>
        <value>hadoop/data/tmp/</value>
    </property>
</configuration>
```
#### HDFS: 
##### hadoop-env.sh 
for JAVA_HOME 
##### hdfs-site.xml 
```xml
<configuration>
    <property>
        <name>dfs.replication</name>
        <value>1</value>
    </property>
</configuration>
```
##### slaves 
配置数据节点名称
```
h1
h2
h3
```
#### yarn: 
##### yarn-env.sh 
for JAVA_HOME
##### yarn-site.xml
```xml
<configuration>
    <!-- reducer获取数据的方式 -->
    <property>
        <name>yarn.nodemanager.aux-services</name>
        <value>mapreduce_shuffle</value>
    </property>
    <!-- 指定resourcemanager的主机地址 -->
    <property>
        <name>yarn.resourcemanager.hostname</name>
        <value>h2</value>
    </property>
</configuration>
```

#### mapreduce: 
##### mapred-env.sh  
for JAVA_HOME
##### mapred-site.xml 
```xml
<configuration>
    <property>
        <name>mapreduce.framework.name</name>
        <value>yarn</value>
    </property>
</configuration>
```

### 在集群上分发以上所有文件
rsync 分发到h2 h3上
### 检查文件分发情况
查看文件是否分发到集群的各个机器上

## 集群的启动
### 第一次启动需要格式化namenode
`bin/hdfs namenode -format ` 

### 启动HDFS
`sbin/start-dfs.sh ` 

### 启动yarn
`sbin/start-yarn.sh ` 

















