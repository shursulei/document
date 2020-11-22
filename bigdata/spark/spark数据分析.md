# SparK快速大数据分析

## 问题总结

行动操作

collect(),实际操作不适用；实际中采用写入到hdfs中或者Amazon s3中



parallelize 方法作用



代码优化点：

Spark中自定义函数，向Spark传递





intersection():通过网络混洗数据来发现共有的元素





数据读取与保持

SequenceFile格式的文件

​    

Spark cache和persist使用场景和区别、广播和累加器使用方式和原理

<https://blog.csdn.net/aijiudu/article/details/55811464>



persist、cache、累加器、广播变量的区别

## Spark RDD基础编程

```python
lines = sc.textFile("file:///opt/spark/README.md")
pythonLines=lines.filter(lambda line: "Python" in line)
pythonLines.first()
#u'high-level APIs in Scala, Java, Python, and R, and an optimized engine that'
pythonLines.persist
<bound method PipelinedRDD.persist of PythonRDD[5] at RDD at PythonRDD.scala:53>
    
    
grep -rn "Python" /opt/spark/README.md
high-level APIs in Scala, Java, Python, and R, and an optimized engine that
44:## Interactive Python Shell
46:Alternatively, if you prefer Python, you can use the Python shell:
1、创建RDD
lines=sc.parallelize(["pandas","i like panads"])
2、转换操作
hadoopLines=lines.filter(lambda line: "Hadoop" in line)
badLinesRDD=pythonLines.union(hadoopLines)
3、行动操作
```





## 数据存储和保存

文件系统

本地、NFS、S3、HDFS、Cassandra、HBase

文件格式

文本文件、JSON、SequenceFile、protocol buffer、CSV

Spark SQL中的结构化数据源

 JSON 和 Apache Hive 在内的结构化数据

  数据库与键值存储
 Cassandra、 HBase、Elasticsearch 以及 JDBC 源。 



|      |      |      |
| ---- | ---- | ---- |
|      |      |      |
|      |      |      |
|      |      |      |

格式名称 结构化 备注
文本文件 否 普通的文本文件，每行一条记录
JSON 半结构化 常见的基于文本的格式，半结构化；大多数库都要求每行一条记录
CSV 是 非常常见的基于文本的格式，通常在电子表格应用中使用
SequenceFiles 是 一种用于键值对数据的常见 Hadoop 文件格式
Protocol buffers 是 一种快速、节约空间的跨语言格式
对象文件 是 用来将 Spark 作业中的数据存储下来以让共享的代码读取。改变类的时候
它会失效，因为它依赖于 Java 序列化 