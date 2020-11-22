# python3实战Spark大数据分析调度

## 前沿

### 1、技术点

- spark+python3,
- 调优及调度框架Azkaban[优点和缺点]；
- 构建大数据平台的技术选型及集群评估

### 2、技术选型

|   技术选型    |    版本     |
| :-----------: | :---------: |
|     linux     | Centos(6.4) |
|    python     |    3.6.5    |
|    Hadoop     |  CDH(5.7)   |
|     Spark     |    2.3.0    |
| ElasticSearch |    6.3.0    |
|    Kibana     |    6.3.0    |
|    Azkaban    |     3.x     |
|      JDK      |     1.8     |
|     Scala     |   2.11.8    |
|     maven     |    3.3.9    |
|   开发工具    |    IDEA     |

## 一、环境搭建

1、用户

hadoop

2、java、scala

3、hadoop、maven

4、python依赖安装



## 二、PySpark-Spark Core之RDD

RDD定义

源码讲解

1、org.apache.spark.rdd.RDD

```scala
abstract class RDD[T: ClassTag](
    @transient private var _sc: SparkContext,
    @transient private var deps: Seq[Dependency[_]]
  ) extends Serializable with Logging 
#1)RDD是一个抽象类
#2）带泛型的，可以支持多种类型： String、Person、User
```

RDD特性

五大特性

```shell
Internally, each RDD is characterized by five main properties
- A list of partitions
#一系列的分区
- A function for computing each split
#每个分区执行分区  y=f(x)
- A list of dependencies on other RDDs(核心)

#rdda=5个partition
==>map
rddb=5个partition

- Optionally, a Partitioner for key-value RDDs (e.g. to say that the RDD is hash-partitioned)

- Optionally, a list of preferred locations to compute each split on (e.g. block locations for  an HDFS file)
 #数据调度所在的节点进行计算：移动数据非移动计算。使用脚本对计算进行调度
 #为什么有多个locations?
 
```

RDD特性在源码中的体现

```scala
/**
 * :: DeveloperApi ::
 * Implemented by subclasses to compute a given partition.
 */
@DeveloperApi
def compute(split: Partition, context: TaskContext): Iterator[T]
#特性二

/**
 * An identifier for a partition in an RDD.
 */
trait Partition extends Serializable {
  /**
   * Get the partition's index within its parent RDD
   */
  def index: Int

  // A better default implementation of HashCode
  override def hashCode(): Int = index

  override def equals(other: Any): Boolean = super.equals(other)
}
#特性一
protected def getPartitions: Array[Partition]
#特性三
protected def getDependencies: Seq[Dependency[_]] = deps
#特性五
protected def getPreferredLocations(split: Partition): Seq[String] = Nil
#特性四
@transient val partitioner: Option[Partitioner] = None

#JDBCRDD
HadoopRDD#需要学习
#HadoopRDD

```

图解RDD

SparkContext&SparkConf

不需要硬编码，使用submit命令传入

第一义务创建SparkContext

​	连接到Spark“集群”：local、standalon、yarn、mesos；

​	通过SparkContext来创建RDD、广播变量到集群

在创建SparkContext之前还需要创建一个SparkConf对象





pyspark脚本分析

​	使用pysaprk执行：存在一个问题：如何设计这个不同用户提交自己的pysaprk，不同用户有自己的UI界面



RDD的创建方式

Parllelized Collections

External Datasets



共享文件系统、HDFS、Hbase、Hadoop InputFormat

一个spark的task对应一个partition

注意：

If using a path on the local filesystem, the file must also be accessible at the same path on worker nodes. Either copy the file to all workers or use a network-mounted shared file system.

​	1）单节点，hello.txt只要在这台几点上就行

​	2）standalone: Spark集群：3个节点 local path 都从节点的本地读取数据





Spark应用程序开发及运行

1)IDE：pycharm
2)设置基本参数: python interceptor  PYTHONPATH    SPARK_HOME    2个zip包

3）开发

4）使用本地测试

提交pyspark应用程序



Spark Core核心RDD

Spark Core RDD编程

RDD常用算子编程详解

​	

> ​	<http://spark.apache.org/docs/latest/rdd-programming-guide.html#rdd-operations>



​	lazy(********)

map/filter/group by/distinct

actions

 count/reduce/collect .......



RDD常用算子

Transformation算子、Action算子

​	map:

​		map(func)

​		将func函数作用到数据集的每一个元素上，生成一个新的分布式的数据集返回

 		map(lambda x:x*2)

​		map(lambda x:(x,1))
​	fliter:

​		fliter(func)

​		选出所有func返回值为true的元素，生成一个新的分布式的数据集返回

​	flatMap

​		flatMap(func)

​		输入的item能够被map到0或者多个item输出，返回值是一个Sequence.

​	groupByKey:把相同的key的数据分发到一起，需要统计

​		["hello spark", "hello world", "hello world"]

​	ReduceByKey():把相同的key的数据分发到一起，并进行相应的计算

​	mapRdd.reduceByKey(lambda a,b:a+b)	

​	

需求：请按wc结果中出现的次数降序排列

​	

​	union

​	distinct

​	join:

​		inner join

​		outer join:left/right/full

Action算子编程

collect

count

take

reduce

saveAsTextfile

foreach



Spark RDD案列实战

1、词频统计案列多角度迭代

​	1）input:1/n文件  文件夹 后缀名

​		hello spark

​		hello hadoop

​		hello welcome	

​	2)开发步骤分析

​		文本内容的每一行转成一个个的单词: flatMap

​		单词===>（单词，1）:map

​		把所有相同单词的计数相加得到的最终的结果:reduceByKey



TopN案列实战

​	1）input:1/n文件  文件夹 后缀名

​	2）求某个维度的topn

​	3)开发步骤分析

​		文本内容的每一行根据需求提取出你所需要的字段

​		单词===>（单词，1）:map

​		把所有相同单词的计数相加得到的最终的结果:reduceByKey

​		取最多出现次数的降序：sortByKey

平均数：统计平均年龄

​	id age

​	3 96

​	4 44

​	5 67

​	6 4

​	7 98

​		开发步骤分析：

​		1）取出年龄 map

​		2) 计算年龄综合 reduce

​		3) 计算记录总数 count

​		4）求平均数

问题：结果输入到mysql





### Spark运行模式

- local模式

  开发

  ​	--master

  ​	--name

  ​	--py-files

   ./spark-submit --master local[2]  --name spark-local /xxx/spark0402.py file://xx/hello.txt  file ://xxx/output

- standalone模式[生产]

  部署安装standalone模式

  ​	注意点：修改slaves文件

  ​			启动spark集群:$SPARK_HOME/sbin/start-all.sh

  ​			ps:要在spark-env.sh中添加JAVA_HOME,否则会报错

   ./spark-submit --master spark://hadoop000:7077 --name spark-local /xxx/spark0402.py file://xx/hello.txt  file ://xxx/output

  ​			如果使用standalone模式，而且你的节点个数大于1的时候，如果你使用本地文件测试，必须保证每个机器上有文件。

  ​	问题：spark提交代码后ui上显示finished,看不到报错信息

  ​				

- yarn模式【生产】

  ​	mapreduce yarn

  ​	spark on yarn 70%

  ​	spark作业客户端，他需要做的事情就是提交作业到yarn上去执行

  ​	yarn vs standalone

  ​		yarn: 你只需要一个节点，然后提交到作业即可，这个是不需要spark集群的（不需要启动master和worker的）

  ​		standalone:你的spark集群上每个节点都需要部署spark，然后需要启动spark集群（需要启动master和worker）

  ​	为什么需要指定HADOOP_CONF_DIR或者YARN_CONF_DIR

  

  ##需要重新操作

  

- mesos模式

- kubernetes模式







### Spark Core进阶

#### 1.1Spark核心概念

| Term            |                                             | 解释                                                         |
| :-------------- | ------------------------------------------- | ------------------------------------------------------------ |
| Application     | 基于spark的应用程序=1 driver +多个executors | User program built on Spark. Consists of a *driver program* and *executors* on the cluster.例如：spark004.py |
| Application jar |                                             |                                                              |
| Driver program  |                                             | The process running the main() function of the application and creating the SparkContext |
| Cluster manager |                                             | An external service for acquiring resources on the cluster (e.g. standalone manager, Mesos, YARN) |
|                 |                                             |                                                              |
|                 |                                             |                                                              |
|                 |                                             |                                                              |



#### 1.2Spark运行架构及注意事项

#### 1.3Spark和hadoop重要概念区分

#### 1.4 Spark Cache详解

#### 1.5 Spark Lineage详解

#### 1.6 Spark Dependency详解

Spark Core调优

## Spark SQL

#### 1.1 Spark SQL概述

​	前世今生

​	SQL:MySQL、Oracle、DB2、SQLServer

​	SQL引擎

​	

| SQL on hadoop      | Hive/Shark/Impala:Cloudera/Presto/Drill/..... |                                        |
| ------------------ | --------------------------------------------- | -------------------------------------- |
| Hive: on MapReduce | SQL==>MapReduce==>Hadoop Cluster              |                                        |
| Shark: on Spark    | 基于Hive源码进行改造                          |                                        |
| Spark SQL:on Spark |                                               | 以上共同点：metastore 信息存放在 mysql |
| Hive on Spark      |                                               |                                        |

​	四点概述：官网

​	Spark SQL不仅仅是SQL这么简单的事情，他还能做更多的事情

​	Hive: SQL

Spark SQL:SQL

Spark SQL提供的操作数据的方式

​	SQL

​	DataFrame API

​	Dataset API

一个用于处理结构化数据的Spark组件，强调的是“结构化数据”，而非“SQL”

#### 1.2 Spark SQL架构



#### 1.3 DataFrame/Dataset详解

DataSet是Spark的1.6版本出现的。Python不支持Dataset api。

DataFrame：以列(列名、列类型、列值)的形式构成分布式的数据库。

面试题：

RDD和DataFrame的区别：1、2、3、4、5



代码测试



## Spark Streaming

#### 1.1 Spark Streaming概述

![1560069481122](D:\Typora\workspace\bigdata\spark\Spark-Streaming流处理.png)

Spark Streamin是Spark Core的一个扩展。主要是针对input==>到output的一个过程。

inputs:Kafka, Flume, Kinesis, or TCP sockets;

outputs: filesystems, databases, and live dashboards；

问题：在线机器学习？？

Q:安装完Spark之后能否直接使用Spark Streaming?[NO]

常用实时流处理框架对比

​	Storm:真正的实时流处理  Tuple；语言：Java；Storm需要搭建集群的；

​	Spark Streaming:并不是真正的实时流处理，而是一个mini batch操作

​			Scala、Java、Python 使用Spark一栈式解决问题

​	Flink：从流处理出发，处理批量计算。与Spark Streaming的底层的逻辑式反过来的

​	kafka Stream



执行原理

![1560070335350](D:\Typora\workspace\bigdata\spark\Spark-Streaming流处理底层原理.png)

Spark Core的核心抽象叫做：RDD 5大特性、对应的源码的5个方法是什么;

Spark Streaming的核心抽象叫做：DStream: a DStream is represented as a sequence of [RDDs](http://spark.apache.org/docs/latest/api/scala/index.html#org.apache.spark.rdd.RDD).

面试题：Spark Core的存储策略和Spark Streaming的存储策略

```python
#socketTextStream源码分析
 def socketTextStream(self, hostname, port, storageLevel=StorageLevel.MEMORY_AND_DISK_2):
        """
        Create an input from TCP source hostname:port. Data is received using
        a TCP socket and receive byte is interpreted as UTF8 encoded ``\\n`` delimited
        lines.

        @param hostname:      Hostname to connect to for receiving data
        @param port:          Port to connect to for receiving data
        @param storageLevel:  Storage level to use for storing the received objects
        """
        jlevel = self._sc._getJavaStorageLevel(storageLevel)
        return DStream(self._jssc.socketTextStream(hostname, port, jlevel), self,
                       UTF8Deserializer())
    
 def textFileStream(self, directory):
        """
        Create an input stream that monitors a Hadoop-compatible file system
        for new files and reads them as text files. Files must be wrriten to the
        monitored directory by "moving" them from another location within the same
        file system. File names starting with . are ignored.
        """
        return DStream(self._jssc.textFileStream(directory), self, UTF8Deserializer())
```

#### 1.2  核心概念

Dstream



#### 1.3 案列实战

## Azkaban调度

​	环境搭建，hadoop作业，hive作业，spark作业，配置告警；生产案列，多个之间的部署，公司业务的定制的解决方案

#### 1.1工作流描述

​	请假、借款。类JAVAEE(JBPM、Activity)

#### 1.2调度在大数据处理中的重要性

​	定时调度、依赖调度

​	Spark SQL/hadoop用于做离线统计处理

​	ETL

​	1）数据抽取:

​		Sqoop把RDBMS中的数据抽取到

​		Flume进行日志、文本数据的采集，采集到Hadoop

​	2) 数据处理

​		Hive/MapReduce/Spark/....

​	3)统计结果入库

​		数据存放到HDFS(Hive/Spark SQL/文件)

​			启动一个Server:HiveServer2 /ThriftServer

​			jdbc的方式去访问统计结果

​		使用Sqoop把结果导出到RDBMS中

​		

#### 1.3常见的调度框架

​	Azkaban:轻量级

​	oozie：重量级

​		cm hue

​		xml

​	宙斯

#### 1.4Azkaban概述

​	Open-source Workflow Manager

​	批处理工作流，用于跑Hadoop的job

​	提供了一个易于使用的用户界面来维护和跟踪你的工作流程

​	特性：见官网

#### 1.5Azkaban架构

​		Relational Database(MySQL)

​		AzkabanWebServer

​		AzkabanExecutorServer

#### 1.6Azkaban运行模式

​	solo-server mode :stand alone;数据信息存储到H2上==>MySQL；webServer和execserver是运行在同一个进程中;

​	the heavier weight two server mode：数据信息存储在MySQL,在生产上一定要做主备；数据是做主备的(master-slave)；webServer和execserver是运行在不同的进程中;

​	distributed multiple-executor mode 

#### 1.7Azkaban源码编译

1)去github上下载源码包

2）./gradlew build installDist

3)建议搭建先去下载gradle-4.1-all.zip

然后整合到azkaban源码中，避免在编译的过程中下载，导致编译速度非常慢

4）编译成功之后，去对应的目录下找到对应的模式的安装包即可

```shell
#准备azkaban-3.65.0、gradle-5.3.1.zip
#/app/azkaban-3.65.0/gradle/wrapper，gradle-5.3.1.zip
#edit gradle-wrapper.properties
distributionUrl=gradle-5.3.1.zip
cd /app/azkaban-3.65.0
./gradlew build installDist
##注意,此处需要安装git

########################此处采用git的源码编译方式#############################
#######################solo-server mode : 单机模式，#######################
```



#### 1.8Azkaban单机部署及快速入门



## 项目实战

#### 1.1企业级大数据开发流程

- ​	项目(产品)调研

  ​		淘宝电商必懂的100个专业术语，CDN

- ​	需求分析

  ​		项目的需求

  ​			显示的需求

  ​			隐式的需求

  ​		甘特图

- ​	方案设计

  ​		概要设计

  ​		详细设计

  ​			基本要求：

  ​			系统要求：系统架构的扩展性，容错性、高可用

- ​	功能开发

- ​	测试

- ​	部署上线

- ​	运维

- ​	后期迭代开发

#### 1.2企业级大数据应用

#### 1.3企业级大数据分析平台

#### 1.4数据量预估及集群规划

#### 1.5项目需求

#### 1.6功能需求

#### 1.7通过Azkaban进行调度

#### 1.8项目总结及后续课程展望

空气环境质量的是否改善，项目架构图



## Spark-Standalone集群搭建

时间同步

```shell
ansible all -m shell -a "date"
```

机器规划

| 功能\机器名 | 192.168.145.102 | 192.168.145.103 | 192.168.145.105 |
| :---------: | :-------------: | :-------------: | :-------------: |
|   master    |      true       |      false      |      false      |
|   worker    |      false      |      true       |      false      |
|   worker    |      false      |      false      |      true       |

### 1.zookeeper集群规划和搭建



```shell
cd /etc/ansible
ansible all -m shell -a "ln -sf /app/zookeeper-3.4.10/ /opt/zookeeper"
ansible all -m shell -a "mkdir -p /opt/zookeeper/tmp/zookeeper"
#echo zoo.cfg
dataDir=/opt/zookeeper/tmp/zookeeper
#echo zoo.cfg
server.1=192.168.145.101:2888:3888
server.2=192.168.145.102:2888:3888
server.3=192.168.145.103:2888:3888
server.4=192.168.145.104:2888:3888
server.5=192.168.145.105:2888:3888
#echo myid
1,2,3,4,5

#edit log's store location
#edit log4j.properties

#2# zookeeper.root.logger=INFO, ROLLINGFILE
#5# zookeeper.log.file=zookeeper-1.log
#36# log4j.appender.ROLLINGFILE=org.apache.log4j.DailyRollingFileAppender
#41# #log4j.appender.ROLLINGFILE.MaxFileSize=10MB

#zkEnv.sh
if [ "x${ZOO_LOG_DIR}" = "x" ]
then
    ZOO_LOG_DIR="/opt/zookeeper/logs"
fi

if [ "x${ZOO_LOG4J_PROP}" = "x" ]
then
    ZOO_LOG4J_PROP="INFO,ROLLINGFILE"
fi


ansible all -m shell -a "/opt/zookeeper/bin/zkSever.sh status"
ansible all -m shell -a "/opt/zookeeper/bin/zkServer.sh start"
```

zookeeper启动报错

Error contacting service. It is probably not running.

解决方法：基本是zoo.cfg和myid的不匹配的问题

创建spark用户

```shell
ansible all -m shell -a "groupadd -g 121 bigdata"
ansible all -m shell -a "useradd spark -g bigdata -d /home/spark -m -u 1401"
```

配置

```shell
ansible spark -m shell -a "cd /opt/spark/conf/;cp spark-env.sh.template spark-env.sh"
#edit spark-env.sh
#edit slaves
#edit /etc/profile
```

启动

使用spark用户启动

```shell
ansible spark -m shell -a "chown -R spark:bigdata /opt/spark"
赋值密码
echo 'spark' |passwd --stdin spark
ssh-keygen
ssh-copy-id 192.168.145.102
ssh-copy-id 192.168.145.103
ssh-copy-id 192.168.145.105
ansible spark -m shell -a "chown -R spark:bigdata /opt/spark/logs"
cd /opt/spark/sbin
先启动./start-all.sh
后启动./start-master.sh
```

