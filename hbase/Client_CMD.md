

```shell
# 启动hbase client help
bin/hbase 

# 启动hbase client shell
bin/hbase shell


# 查看命名空间
list_namespace 

# 查看表
list_namespace_tables 'default' 


# 创建命名空间
create_namespace 'ns1'


# 创建表  ns1 -- 命名空间， t1 -- 表名  后面的是列簇
create 'ns1:t1', {NAME => 'f1', VERSION =>5}
# 
hbase> create 't1', {NAME => 'f1'}, {NAME => 'f2'}, {NAME => 'f3'}
hbase> # The above in shorthand would be the following:
hbase> create 't1', 'f1', 'f2', 'f3'
hbase> create 't1', {NAME => 'f1', VERSIONS => 1, TTL => 2592000, BLOCKCACHE => true}
hbase> create 't1', {NAME => 'f1', CONFIGURATION => {'hbase.hstore.blockingStoreFiles' => '10'}}


# 扫描表 scan 
hbase> scan 'hbase:meta'
hbase> scan 'hbase:meta', {COLUMNS => 'info:regioninfo'}
hbase> scan 'ns1:t1', {COLUMNS => ['c1', 'c2'], LIMIT => 10, STARTROW => 'xyz'}
hbase> scan 't1', {COLUMNS => ['c1', 'c2'], LIMIT => 10, STARTROW => 'xyz'}
hbase> scan 't1', {COLUMNS => 'c1', TIMERANGE => [1303668804, 1303668904]}
hbase> scan 't1', {REVERSED => true}
hbase> scan 't1', {FILTER => "(PrefixFilter ('row2') AND
(QualifierFilter (>=, 'binary:xyz'))) AND (TimestampsFilter ( 123, 456))"}


# 写入 put
# r1 rowKey 行键
# c1 column 列
# value 值
hbase> put 'ns1:t1', 'r1', 'c1', 'value'
hbase> put 't1', 'r1', 'c1', 'value'
hbase> put 't1', 'r1', 'c1', 'value', ts1
hbase> put 't1', 'r1', 'c1', 'value', {ATTRIBUTES=>{'mykey'=>'myvalue'}}
hbase> put 't1', 'r1', 'c1', 'value', ts1, {ATTRIBUTES=>{'mykey'=>'myvalue'}}
hbase> put 't1', 'r1', 'c1', 'value', ts1, {VISIBILITY=>'PRIVATE|SECRET'}


# 查询数据
# 查询某个rowkey的数据
get 'ns1:stuInfo', '1001'    
# 查询某个列的数据
get 'ns1:stuInfo', '1001', 'info:name'     
# 查询某个列簇的数据
get 'ns1:stuInfo', '1001', 'info'


# 删除数据
# delete 'ns1:t1', 'r1', 'c1', ts1  -- 行键，列，时间戳
delete 'ns1:stuInfo', '1001', 'info:name'

# deleteall 删除rowkey的数据
deleteall 'ns1:stuInfo', '1001' 




# 清空表 (1.disable table 2.drop table 3.Create table)
truncate 'ns1:stuInfo' 


# 删除表 drop 
drop 'ns1:stuInfo'

# 删除前先要disable table
disable 'ns1:stuInfo'



```

#### 实例
```shell
create_namespace 'ns1'

create 'ns1:stuInfo', 'info', 'contact'

put 'ns1:stuInfo', '1001', 'info:name', 'Jack'
put 'ns1:stuInfo', '1001', 'info:age', '18'
put 'ns1:stuInfo', '1001', 'info:gender', 'male'
put 'ns1:stuInfo', '1001', 'info:addr', 'beijing'
put 'ns1:stuInfo', '1001', 'contact:phone', '13112340987'

scan 'ns1:stuInfo' 
# ---------------- RESULT ------------------------------------------------------------------------
# ROW                                  COLUMN+CELL                                                                                               
#  1001                                column=contact:phone, timestamp=1572576705389, value=13112340987                                          
#  1001                                column=info:addr, timestamp=1572576703977, value=beijing                                                  
#  1001                                column=info:age, timestamp=1572576703873, value=18                                                        
#  1001                                column=info:gender, timestamp=1572576703920, value=male                                                   
#  1001                                column=info:name, timestamp=1572576703800, value=Jack                                                     
# 1 row(s) in 0.0550 seconds


put 'ns1:stuInfo', '1003', 'contact:email', 'x1@qq.com'
put 'ns1:stuInfo', '1003', 'info:name', 'Mary'
put 'ns1:stuInfo', '1003', 'info:age', '19'
put 'ns1:stuInfo', '1003', 'info:gender', 'female'

scan 'ns1:stuInfo' 
# ---------------- RESULT ------------------------------------------------------------------------
# ROW                                  COLUMN+CELL                                                                                               
#  1001                                column=contact:phone, timestamp=1572576705389, value=13112340987                                          
#  1001                                column=info:addr, timestamp=1572576703977, value=beijing                                                  
#  1001                                column=info:age, timestamp=1572576703873, value=18                                                        
#  1001                                column=info:gender, timestamp=1572576703920, value=male                                                   
#  1001                                column=info:name, timestamp=1572576703800, value=Jack                                                     
#  1003                                column=contact:email, timestamp=1572576765942, value=x1@qq.com                                            
#  1003                                column=info:age, timestamp=1572576766129, value=19                                                        
#  1003                                column=info:gender, timestamp=1572576766750, value=female                                                 
#  1003                                column=info:name, timestamp=1572576766100, value=Mary                                                     
# 2 row(s) in 0.0500 seconds


put 'ns1:stuInfo', '1002', 'info:name', 'Leo'
put 'ns1:stuInfo', '1002', 'info:age', '20'
put 'ns1:stuInfo', '1002', 'info:gender', 'male'


scan 'ns1:stuInfo' 
# ---------------- RESULT ------------------------------------------------------------------------
# 动态列  行键是自动排序的
# ROW                                  COLUMN+CELL                                                                                               
#  1001                                column=contact:phone, timestamp=1572576705389, value=13112340987                                          
#  1001                                column=info:addr, timestamp=1572576703977, value=beijing                                                  
#  1001                                column=info:age, timestamp=1572576703873, value=18                                                        
#  1001                                column=info:gender, timestamp=1572576703920, value=male                                                   
#  1001                                column=info:name, timestamp=1572576703800, value=Jack                                                     
#  1002                                column=info:age, timestamp=1572576828773, value=20                                                        
#  1002                                column=info:gender, timestamp=1572576829469, value=male                                                   
#  1002                                column=info:name, timestamp=1572576828572, value=Leo                                                      
#  1003                                column=contact:email, timestamp=1572576765942, value=x1@qq.com                                            
#  1003                                column=info:age, timestamp=1572576766129, value=19                                                        
#  1003                                column=info:gender, timestamp=1572576766750, value=female                                                 
#  1003                                column=info:name, timestamp=1572576766100, value=Mary                                                     
# 3 row(s) in 0.1040 seconds


```





























































































































































