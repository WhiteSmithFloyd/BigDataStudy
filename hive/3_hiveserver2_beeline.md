
# hive server 2 服务介绍

1. 将hive变成一个服务对外开放，通过客户端去连接

 + 启动服务   `bin/hiveserver2`


 +  启动客户端  `bin/beeline`  可以通过 `beeline` 来发起 `mapreduce` 和 `yarn` 任务 

    ​

2. 连接databse

   帮助信息 bin/beeline --help

   `! connect jdbc:hive2://bigdata-05:10000`

   或者

   `bin/beeline -u jdbc:hive2://bidgdata01:10000 -n user -p passwd `

   > 在操作beeline的时候，hiveserver2会有回显信息，操作成功会有ok，失败会有错误信息
   >
   > 所以在开启`bin/hiveserver2` 的时候，使用 `nohup` 让进程在后台启动，并且把回显数据添加到文本中
   >
   > ```shell
   > nohup bin/hiveserver2 > myout.file 2>&1 &
   > ```

   > hiveserver2如果连接不上，超时，可以修改参数，把L去掉，添加到`hive-site.xml` 中
   >
   > ```xml
   > <property>
   >   <name>hive.server2.long.polling.timeout</name>
   >   <value>5000L</value>
   >   <description>...</description>
   > </property>
   > ```

   > hiveserver2 默认没有开启权限验证，但是配置文件中有相关的设置
   >
   > ```xml
   > <property>
   >   <name>hive.server2.authentication</name>
   >   <!-- nosasl, none, ldap, kerberos, pam, custom -->
   >   <value>NONE</value>
   > </property>
   > ```





