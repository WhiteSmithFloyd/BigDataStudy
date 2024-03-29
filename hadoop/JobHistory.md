Hadoop中的jobhistory配置与启动停止
jobhistory配置
```xml
在yarn-site.xml中添加
<!-- 开启日志聚合 -->
<property>
<name>yarn.log-aggregation-enable</name>
<value>true</value>
</property>
<property>
<name>yarn.log-aggregation.retain-seconds</name>
<value>106800</value>
</property>
```

2. 在mapred-site.xml中添加
```xml
<!-- 设置jobhistoryserver 没有配置的话 history入口不可用 -->
<property>
    <name>mapreduce.jobhistory.address</name>
    <value>localhost:10020</value>
</property>

<!-- 配置web端口 -->
<property>
    <name>mapreduce.jobhistory.webapp.address</name>
    <value>localhost:19888</value>
</property>

<!-- 配置正在运行中的日志在hdfs上的存放路径 -->
<property>
    <name>mapreduce.jobhistory.intermediate-done-dir</name>
    <value>/history/done_intermediate</value>
</property>

<!-- 配置运行过的日志存放在hdfs上的存放路径 -->
<property>
    <name>mapreduce.jobhistory.done-dir</name>
    <value>/history/done</value>
</property>
```

jobhistory的启动与停止
启动： 在hadoop/sbin/目录下执行 
`./mr-jobhistory-daemon.sh start historyserver`

停止：在hadoop/sbin/目录下执行 
`./mr-jobhistory-daemon.sh stop historyserver`
