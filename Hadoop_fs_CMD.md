# Hadoop 常用命令


hadoop fs -help [cmd]
```shell
hadoop fs -help ls
```

```shell
hadoop fs -ls 
```
```shell
hadoop fs -mkdir
```

```shell
hadoop fs -moveFromLLocal 
```
从本地剪切并粘贴到集群中

```shell
hadoop fs -moveToLLocal
```

```shell
hadoop fs -appendToFile
```
追加到一个已经存在的文件末尾

```shell
hadoop fs -cat
```

```shell
hadoop fs -tail 
```
显示文件的末尾

```shell
hadoop fs -text
```
以文字形式打印一个文件的内容

```shell
hadoop fs -chgrp -chmod -chown
```
和linux一模一样

```shell
hadoop fs -copyFromLocal
```
```shell
hadoop fs -copyToLocal
```

```shell
hadoop fs -cp
```
从hdfs的一个路径拷贝到hdfs的另一个路径上

```shell
hadoop fs -mv
```

```shell
hadoop fs -get 
```
等同于 copyToLocal

```shell
hadoop fs -getmerge
```
合并并下载多个文件

```shell
hadoop fs -put
```
```shell
hadoop fs -rm
```
```shell
hadoop fs -rmdir
```

```shell
hadoop fs -df
```
统计文件系统的可用空间信息 -h：打印格式

```shell
hadoop fs -du
```
统计文件夹大小信息 -s:总大小

```shell
hadoop fs -count
```
统计指定目录下的文件节点数量

```shell
hadoop fs -setrep
```
设置hdfs中文件的副本数量



