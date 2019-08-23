# Hive 2 Base Operation

创建表的三种方式：

+ 普通创建

  ```sql
  create table student (
  	id int,
    name string
  ) row format delimited fields terminated by '\t' ;
  ```

+ 子查询方式 (抽取源表的部分字段分子数据)

  将子查询出来的结果赋予一张新的表 (数据就会存在于新的表当中)

  ```sql
  create table student_names as select name from student ;
  create table student_bk as select * from student ;
  ```

+ like方式

  只是将表结构完全复制一份，不包含数据

  ```sql
  # like existing_table_or_view_name 
  create table stu_like like student ;
  ```

  ​

## hive 的四大表类型

总体上分为，`外部表`、`内部表`、`分区表`、`桶表`

他们可以相互结合。

### 外部表 VS 内部表 , 区别

创建两张一样的表，加载一样的数据，有一张是外部表，一张是内部表（manager）

如果是外部表，删除表的时候，只删除表的元数据信息（即，只去mysql中删除数据）

> #### 创建外部表
>
> create external table  dept (...

如果是内部表，删除表的时候，元数据信息和数据全部删除（mysql和hdfs上都删除）

> 普通创建语句所创建的即为内部表



### 外部表的使用

当一份数据被公用的时候，不同小组创建不同的外部表来对这份数据进行查询或insert into等操作，这时候可以使用外部表，当执行完毕就可以删除，且不会影响到数据

一般尝尝配合location使用。

```sql
create external table  dept (
	dept_no int,
	dept_name string
) row format delimited fields terminated by '\t' 
location '/home/hive/warehouse/data/dept_exter' ;
# 这样就把数据直接加载上了 
```



 ###  分区表

`[PARTITIONED BY (col_name data_type [COMMENT col_comment], ... )]`

+ 普通的表： select * from logs where data = '20171209' 

  执行流程：对全表的数据进行查询，然后才进行过滤操作

+ 分区表： select * from where date= '20171209' 

  执行的流程：直接加载对应文件路径下的数据

分区字段是逻辑性的，体现在hdfs上形成一个文件夹存在，并不在数据中，必须不能是数据中已包含的字段



#### 一级分区

创建分区表

```sql
create table emp_part (
	emp_no int,
  en_name string,
  job string,
  mgr int,
  hiredate string,
  sal double,
  comm double,
  dept_no int
) partitioned by (`date` string)
row format delimited fields terminated by '\t' ;

#
load data local inpath '/opt/datas/emp.txt' into table emp_part partition(`date`='20171209') ;
load data local inpath '/opt/datas/emp.txt' into table emp_part partition(`date`='20171208') ;

#
select * from emp_part where `date`='20171209' ;

```



#### 二级分区

创建分区表：

```sql
create table emp_part2 (
	emp_no int,
  en_name string,
  job string,
  mgr int,
  hiredate string,
  sal double,
  comm double,
  dept_no int
) partitioned by (`date` string, hour string)
row format delimited fields terminated by '\t' ;

#
load data local inpath '/opt/datas/emp.txt' into table emp_part partition(`date`='20171209', hour='01') ;
load data local inpath '/opt/datas/emp.txt' into table emp_part partition(`date`='20171209', hour='02') ;

#
select * from emp_part where `date`='20171209' ;
select * from emp_part where `date`='20171209' and hour = '01' ;
```



### 桶表

`[CLUSTERED BY (col_name, col_name, ...) [SORTED BY (col_name [ASC|DESC], ...) ] INTO num_buckets BUCKETS]`

`set hive.enforce,bucketing = true ;`

一般使用在发生数据倾斜的场景

```sql
create table emp_bu ()
```



### HQL

hive支持常用的HQL语句

1.过滤

```sql
-- where : select, update,  delete, insert into
select  * from emp where sal > 3000

-- limit 
select * from emp limit 1;

-- distinct -- map reduce
select distinct deptno from emp ;

-- 使用 group by 去重
select min(deptno), deptno from emp group by deptno ;

-- between and (等于也能拿到)
select * from emp where sal between 2000 and 3000 ;

-- is null & is not null
select ename from emp where comm is null ; 

-- having 
select deptno, avg(sal) as avg_sal from emp group by deptno having avg_sal > 2000 ;
select deptno, avg from (select deptno, avg(asl) as avg from emp group by deptno ) a where avg > 2000; 

```

注意：

1. 不能太多层子查询
2. where语句不能嵌套子查询
3. 子查询不能做字段



聚合函数

count  sum  



### 窗口函数

窗口函数 ： 做单独的窗口老计算这列数据的值 -- 新增了一列

```sql
(ROW | RANGE) BETWEEN ( UNBOUNDED | [NUM]) PRECEDING AND ([NUM] PRECEDING | CURRENT ROW | (UNBOUNDED | [NUM]) FOLLOWING) 
```

+ row ： 数据的物理行
+ range： 范围（对某一列的值设定范围）

| 名称                  | 解释      |
| ------------------- | ------- |
| unbounded preceding | 第一行     |
| preceding           | 当前行的前一行 |
| current row         | 当前行     |
| following           | 当前行的下一行 |
| unbounded following | 数据的最后一行 |

实例

```sql
-- 使用窗口获得最大薪水的员工

select *, max(sal) over (partition by deptno order by sal desc range between unbounded preceding and current row) as max_window from emp ;


```



### UDF和beeline

用户自定函数分类： 

+ udf  用户自定义函数，格式转化，一行对一行
+ udaf 多行数据输入，一条数据输出
+ udtf 一条数据输入，多条数据输出

```java
// java 代码参见网上
public class MyUDF extends UDF {
    // TODO ...
}
```

























































