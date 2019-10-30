# Zookeeper Installation

### 解压

```shell
tar -zxvf zookeeper-3.4.14.tar.gz  -C [安装目录]
```



### 配置文件

####  `zoo.cfg` 文件

```shell
cp zoo-sample.cfg zoo.cfg
vim zoo.cfg
```

修改的地方有两处

+ dataDir： 指定zookeeper将数据保存在哪个目录下

+ 完全分布式： 

  ```shell
  server.1=xxx.xxx.xxx.xxx:2888:3888
  server.2=xxx.xxx.xxx.xxx:2888:3888
  server.3=xxx.xxx.xxx.xxx:2888:3888
  ```

  ​

#### myid文件

 到之前配置的zookeeper数据文件所在的目录下生成一个文件叫`myid`，其中写上一个数字表明当前机器是哪一个编号的机器

```shell
vim myid
1
```



### 拷贝

将zookeeper文件夹copy到其他zk的服务器上 `scp -r zookeeper-3.4.14 root@yun02` ，然后修改`myid`中的`id`即可



### 启动zookeeper

**Zookeeper集群需要每台挨个启动**。

```shell
#启动ZK服务: 
bin/zkServer.sh start
#停止ZK服务: 
bin/zkServer.sh stop
#重启ZK服务: 
bin/zkServer.sh restart
#查看ZK服务状态: 
bin/zkServer.sh status
```

> 启动集群的时候，集群数量启动没有超过一半，状态会有错误提示，当集群启动数量超过一半就会自动转为正常状态，并且**此台使集群进入正常工作状态的服务器会成为leader角色**，集群中其他服务器的角色为fllower。















