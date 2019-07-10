# Hadoop 常用命令

hadoop fs -help [cmd]
``hadoop fs -help ls``

``hadoop fs -ls ``
``hadoop fs -mkdir``

``hadoop fs -moveFromLLocal ``
从本地剪切并粘贴到集群中

``hadoop fs -moveToLLocal``

``hadoop fs -appendToFile``
追加到一个已经存在的文件末尾

``hadoop fs -cat``

``hadoop fs -tail ``
显示文件的末尾

``hadoop fs -text``
以文字形式打印一个文件的内容

``hadoop fs -chgrp -chmod -chown``
和linux一模一样

``hadoop fs -copyFromLocal``
``hadoop fs -copyToLocal``

``hadoop fs -cp``
从hdfs的一个路径拷贝到hdfs的另一个路径上

``hadoop fs -mv``

``hadoop fs -get ``
等同于 copyToLocal

``hadoop fs -getmerge``
合并并下载多个文件

``hadoop fs -put``
``hadoop fs -rm``
``hadoop fs -rmdir``

``hadoop fs -df``
统计文件系统的可用空间信息 -h：打印格式

``hadoop fs -du``
统计文件夹大小信息 -s:总大小

``hadoop fs -count``
统计指定目录下的文件节点数量

``hadoop fs -setrep``
设置hdfs中文件的副本数量


