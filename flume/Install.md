
### 环境变量
`/etc/profile` 中添加环境变量

### 配置Java环境变量
修改 `flume-env.sh`文件
# JAVA_HOME

### 配置 source, channel, sink 
e.g. `flume-logger.properties`
```properties
# Name the components on this agent
a1.sources = r1
a1.sinks = k1
a1.channels = c1

# Describe/configure the source
a1.sources.r1.type = netcat
a1.sources.r1.bind = h2
a1.sources.r1.port = 44444

# Describe the sink
a1.sinks.k1.type = logger

# Use a channel which buffers events in memory
a1.channels.c1.type = memory
a1.channels.c1.capacity = 1000
a1.channels.c1.transactionCapacity = 100

# Bind the source and sink to the channel
a1.sources.r1.channels = c1
a1.sinks.k1.channel = c1

```

### 启动flume
```shell
bin/flume-ng agent --name a1 --conf conf --conf-file conf/flume-logger.properties \
-Dflume.root.logger=INFO,console 
```
