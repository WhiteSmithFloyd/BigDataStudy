# Hive

### Hive的安装

1. 下载apache-hive.1.2.2.tar.gz，然后解压

2. 修改配置文件

   conf/hive-env.sh

   ```shell
   # Set HADOOP_HOME to point to a specific hadoop install directory
    HADOOP_HOME=$HADOOP_PATH

   # Hive Configuration Directory can be controlled by:
    export HIVE_CONF_DIR=/home/weapon/apache-hive-1.2.2/conf
   ```

3. 运行hadoop(HDFS、YARN)

4. 运行Hive

   ```shell
   ./bin/hive  
   ```

5. 执行命令

   ```sql
   show databases;
   show tables;
   ```

   ​

### 数据类型

数据类型

数值型： tinyint, smallint, int , bigint  

字符型： varchar, char, string 

时间型：date, timestamp

其他类型： boolean

复杂类型：arrays（下标从0开始）、map(key, value)、 structs

> userlist map(int, string)  userlist['1']取出value
>
> user structs<user:string, age:int>   user.name  user.age 



创建表

+ 如果数据的分割符与表的分隔符不一致，就会分析数据异常
+ 默认的分隔符 \001(^A) 使用 ctrl+v ctrl+a 打出来
+ 可以指定分隔符： "\t", ",", " ", ^A, ^B

```sql
create table test (
	id int,
  	name string
);
create table test2 (
	id int,
  	name varchar(20)
) ROW FORMAT DELIMITED FIELDS TERMINATED BY '\t' ;
```



导入本地数据文件

```sql
load data local inpath '/home/data/test' into table test2 ; 
```



## 替换Derby数据库，改用MySQL

1. 安装MySQL数据库

2. 修改配置文件

   hive-site.xml

   ```xml
   <?xml version="1.0" encoding="UTF-8" standalone="no"?>
   <?xml-stylesheet type="text/xsl" href="configuration.xsl"?>
   <configuration>
     <property>
       <name>javax.jdo.option.ConnectionURL</name>
       <value>jdbc:mysql://localhost:3306/hive?createDatabaseIfNotExist=true&amp;useSSL=false</value>
     </property>
       <property>
       <name>javax.jdo.option.ConnectionDriverName</name>
       <value>com.mysql.jdbc.Driver</value>
     </property>
       <property>
       <name>javax.jdo.option.ConnectionUserName</name>
       <value>root</value>
     </property>
       <property>
       <name>javax.jdo.option.ConnectionPassword</name>
       <value>123456</value>
     </property>
   </configuration>
   ```



3. 添加mysql驱动jar到lib中
4. 重启hive





## Hive的操作命令

表描述

```sql
desc  table__name

desc  extended table_name

desc  formatted table_name  (推荐)

```



修改表

```sql
alter table old_table_name  rename to  new_table_name
```



添加一列

```sql
alter table table_name add columns (age int comment 'this is age' )
```



修改列的名字或类型

```sql
alter  table  table_name  change  col_old_name  col_new_name  column_type
# 将c改为c1，并且放在第一列
alter table test_change change c c2 int first ;
# 将a1改为a2，并且类型为string 并放在b的后面
alter table test_change change a1 a2 string after b ;
```

替换列

```sql
# 不能对指定列进行替换，替换的话必须是全表替换
alter table table_name replace columns (col_name data_type)
# 
alter table test_change replace columns (d int, f string);

```

清空表数据

```sql
truncate table table_name [partition partition_spec]
# 只清空数据，表和表结构还存在
truncate table test;
```

删除数据表

```sql
drop table table_name;
```

删除数据库

```sql
#
drop database database_name; 
# 强制删除库
drop database database_name cascade ;
```

查看hive自带的函数

```sql
show functions;
# 查看表述函数信息
desc function count ;
desc function extended count;  
```



## hive的日常配置和linux命令选项

### hive日志

+ 默认路径: &#36; {java.io.tmpdir}/ &#36; {user.name}
+ 修改hive-log4j.properties : `hive.log.dir=/opt/apps/hive-1.2.1/logs`



显示当前数据库

```xml
<propertiy>
	<name>hive.cli.print.current.db</name>
  	<value>true</value>
</propertiy>
<propertiy>
	<name>hive.cli.print.header</name>
  	<value>true</value>
</propertiy>
```

 

指定登录到某个数据库

```shell
bin/hive --database hadoop22
```



指定一个SQL语句，去执行，SQL语句必须用引号包裹起来

```shell
bin/hive -e 'select * from hadoop22.test' 
```



指定一个包含SQL语句的文件，去执行

```shell
bin/hive -f /opt/datas/test.sql 
```



使用重定向将结果保存到某个文件

```shell
bin/hive -f /opt/datas/test.sql >> /opt/datas/hive.txt
```



临时修改配置信息参数的值

```shell
bin/hive --hiveconf hive.cli.print.current.db=true 
```



在hive客户端中去修改参数信息

```sql
set hive.cli.print.current.db;   -- 查看
set hive.cli.print.current.db=false;  --修改
```



自定义数据仓库的位置location

```sql
# create database database_name [LOCATION hdfs_path]
create database hive_db location "/hive_test"
```



查看其他数据库下的所有表

``show tables in database_name;``



常用的shell:  `!` 和 `dfs`

```shell
# ! 表示后面的命令是运行在本地linux文件系统中的
! ls /home/hadoop

# dfs 表示后面的命令是运行在HDFS文件系统的
dfs -ls /

```























































