# Oozie Installation

> #### 一、将`Oozie`安装包上传到`Master`节点并解压

​       

```shell
$ sudo tar -zxvf oozie-4.1.0-cdh5.14.0.tar.gz -C /opt/cludera/
$ cd /opt/cludera/
$ sudo mv oozie-4.1.0-cdh5.14.0/ oozie
$ sudo chown -R hadoop ./oozie/
```

> #### 二、为 `oozie` 分配 `hdfs` 的权限

​       为`oozie`分配`hdfs`的权限，编辑集群上所有机器上的 `core-site.xml` 文件 ，增加如下配置：

```xml
<property>
      <name>hadoop.proxyuser.hadoop.hosts</name>
      <value>*</value>
</property>
<property>
      <name>hadoop.proxyuser.hadoop.groups</name>
      <value>*</value>
</property>
```

> #### 三、解压`oozie`目录下的`hadooplibs.tar.gz`,获取`hadoop`的`jar` 包,在`hadooplibs`目录下

```shell
$ cd oozie/
$ sudo tar -zxvf oozie-hadooplibs-4.1.0-cdh5.14.0.tar.gz
### 在解压的目录下./oozie-4.1.0-cdh5.14.0/hadooplibs
$ sudo mkdir libext
$ sudo cp -r oozie-4.1.0-cdh5.14.0/hadooplibs/hadooplib-2.6.0-cdh5.14.0.oozie-4.1.0-cdh5.14.0/* libext/
```

> #### 三、复制  `ExtJS 2.2`到`/oozie/libext` 目录下

下载`ExtJs`的包：<http://archive.cloudera.com/gplextras/misc/ext-2.2.zip>

```shell
$ sudo cp ext-2.2.zip /opt/cloudera/oozie/libext/
```

> #### 四、设置`Oozie`使用的数据库(`MySQL`)

​       这里提到的数据库是关系型数据库，用来存储`Oozie`的数据。`Oozie`自带一个`Derby`，不过`Derby`只能拿来做实验，在实际应用中不适用。这里我选择`mysql`作为`Oozie`的数据库，接下来就是创建`Oozie`用的数据库

```sql
mysql> create database oozie;    
-- 这个oozie数据库与oozie-site.xml中localhost:3306/oozie的hive对应，用来保存oozie元数据

mysql> grant all on *.* to oozie@localhost identified by 'oozie';   
-- 将所有数据库的所有表的所有权限赋给oozie用户，后面的oozie是配置oozie-site.xml中配置的连接密码

mysql> grant all on *.* to oozie@% identified by 'oozie';   
-- 将所有数据库的所有表的所有权限赋给oozie用户，后面的oozie是配置oozie-site.xml中配置的连接密码

mysql> flush privileges;  
-- 刷新mysql系统权限关系表

```

`oozie-site.xml `配置 `mysql` 的连接属性：

```xml
<property>
        <name>oozie.service.JPAService.jdbc.driver</name>
        <value>com.mysql.jdbc.Driver</value>
</property>
<property>
        <name>oozie.service.JPAService.jdbc.url</name>
        <value>jdbc:mysql://localhost:3306/oozie</value>
</property>
<property>
        <name>oozie.service.JPAService.jdbc.username</name>
        <value>oozie</value>
</property>
<property>
        <name>oozie.service.JPAService.jdbc.password</name>
        <value>oozie</value>
</property>
```

将`mysql`的`jar`包添加到`oozie`的`lib`目录下：

```shell
$ sudo cp mysql-connector-java-5.1.45-bin.jar /opt/cloudera/oozie/lib
```

> #### 五、将jar包打包到Oozie的war包中

​       使用`oozie-setup.sh` 脚本配置`Oozie`，将所有的组件添加到`libext/` 目录下。
打包成`jar`包的命令如下：

```shell
$ bin/oozie-setup.sh prepare-war 
```

结果展示：

```verilog
...(此处省略若干行)

INFO: Adding extension: /opt/cloudera/oozie/libext/jetty-util-6.1.26.cloudera.4.jar
INFO: Adding extension: /opt/cloudera/oozie/libext/jsr305-3.0.0.jar
INFO: Adding extension: /opt/cloudera/oozie/libext/leveldbjni-all-1.8.jar
INFO: Adding extension: /opt/cloudera/oozie/libext/log4j-1.2.17.jar
INFO: Adding extension: /opt/cloudera/oozie/libext/netty-3.10.5.Final.jar
INFO: Adding extension: /opt/cloudera/oozie/libext/netty-all-4.0.23.Final.jar
INFO: Adding extension: /opt/cloudera/oozie/libext/paranamer-2.3.jar
INFO: Adding extension: /opt/cloudera/oozie/libext/protobuf-java-2.5.0.jar
INFO: Adding extension: /opt/cloudera/oozie/libext/servlet-api-2.5.jar
INFO: Adding extension: /opt/cloudera/oozie/libext/slf4j-api-1.7.5.jar
INFO: Adding extension: /opt/cloudera/oozie/libext/slf4j-log4j12-1.7.5.jar
INFO: Adding extension: /opt/cloudera/oozie/libext/snappy-java-1.0.4.1.jar
INFO: Adding extension: /opt/cloudera/oozie/libext/stax-api-1.0-2.jar
INFO: Adding extension: /opt/cloudera/oozie/libext/xercesImpl-2.10.0.jar
INFO: Adding extension: /opt/cloudera/oozie/libext/xml-apis-1.4.01.jar
INFO: Adding extension: /opt/cloudera/oozie/libext/xmlenc-0.52.jar
INFO: Adding extension: /opt/cloudera/oozie/libext/xz-1.0.jar
INFO: Adding extension: /opt/cloudera/oozie/libext/zookeeper-3.4.5-cdh5.14.0.jar

New Oozie WAR file with added 'ExtJS library, JARs' at /opt/cloudera/oozie/oozie-server/webapps/oozie.war


INFO: Oozie is ready to be started
```

查看生成的`war`包，由上面的输出信息可知，war包在目录：`/opt/cloudera/oozie/oozie-server/webapps/oozie.war`：

```shell
$ cd oozie-server/webapps/
$ ls
oozie.war  ROOT
```

> #### 六、`bin/oozie-setup.sh` 使用讲解

```shell
bin/oozie-setup.sh prepare-war [-d directory] [-secure]
                     sharelib create -fs <FS_URI> [-locallib <PATH>]
                     sharelib upgrade -fs <FS_URI> [-locallib <PATH>]
                     db create|upgrade|postupgrade -run [-sqlfile <FILE>]
```

参数说明：

> ###### 1. `sharelib create -fs <FS_URI> [-locallib <PATH>]`

上传新的`sharelib`到`hdfs`上
​       第一个参数：`hdfs` 的URI
​       第二个参数：指定lib目录，通常要指定一下。`oozie-sharelib-4.1.0-cdh5.14.0-yarn.tar.gz`（`maprecude2`版本，我们需指定的），`oozie-sharelib-4.1.0-cdh5.14.0.tar.gz`（`maprecude1`版本，我们不需要）
具体执行命令如下：

```shell
$ bin/oozie-setup.sh sharelib create -fs hdfs://Master:9000  -locallib 
 oozie-sharelib-4.1.0-cdh5.14.0-yarn.tar.gz
```

运行结果显示：

```shell
setting CATALINA_OPTS="$CATALINA_OPTS -Xmx1024m"
log4j:WARN No appenders could be found for logger (org.apache.hadoop.util.Shell).
log4j:WARN Please initialize the log4j system properly.
log4j:WARN See http://logging.apache.org/log4j/1.2/faq.html#noconfig for more info.
SLF4J: Class path contains multiple SLF4J bindings.
SLF4J: Found binding in [jar:file:/opt/cloudera/oozie/libtools/slf4j-simple-1.7.5.jar!/org/slf4j/impl/StaticLoggerBinder.class]
SLF4J: Found binding in [jar:file:/opt/cloudera/oozie/libtools/slf4j-log4j12-1.7.5.jar!/org/slf4j/impl/StaticLoggerBinder.class]
SLF4J: Found binding in [jar:file:/opt/cloudera/oozie/libext/slf4j-log4j12-1.7.5.jar!/org/slf4j/impl/StaticLoggerBinder.class]
SLF4J: See http://www.slf4j.org/codes.html#multiple_bindings for an explanation.
SLF4J: Actual binding is of type [org.slf4j.impl.SimpleLoggerFactory]
the destination path for sharelib is: /user/hadoop/share/lib/lib_20180815192828
```

由上述结果可直，在`hdfs`的路径：`/user/hadoop/share/lib/lib_20180815192828` 生成了jar包

![img](http://upload-images.jianshu.io/upload_images/13521257-852d0973887597c0.png?imageMogr2/auto-orient/strip|imageView2/2/w/1200/format/webp)

hdfs.png

> ###### 2.  `sharelib upgrade -fs <FS_URI> [-locallib <PATH>]`

Upgrade 命令已经被抛弃了, 可以使用create 命令去创建新版本的`sharelibone`

> ###### 3. ` db create|upgrade|postupgrade -run [-sqlfile <FILE>]`

创建`Oozie`数据库，可使用如下命令进行创建：

```shell
./bin/ooziedb.sh create -sqlfile oozie.sql -run DB Connection
```

运行结果如下：

```shell
setting CATALINA_OPTS="$CATALINA_OPTS -Xmx1024m"

Validate DB Connection
...
Oozie DB has been created for Oozie version '4.1.0-cdh5.14.0'

The SQL commands have been written to: oozie.sql
```

在`Oozie`目录下，会生成一个`oozie.sql`文件。



> #### 六、启动Oozie

```shell
$ ./bin/oozied.sh start
```

其实就是启动一个`tomcat`, `jps`一下会发现多了一个进程`BootStrap`.
在浏览器查看`Oozie`,输入网址：Master的`IP:11000`

![img](http://upload-images.jianshu.io/upload_images/13521257-088f6522e1429598.png?imageMogr2/auto-orient/strip|imageView2/2/w/1200/format/webp)

oozie.png

至此，`Oozie`已经成功安装！
