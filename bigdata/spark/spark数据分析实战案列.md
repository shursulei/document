# 编程实战

shell方式

```shell
./spark-shell --master spark://datanode1:7077 --executor-memory 1024m --driver-memory 1024m
```

## 1.1 分区

```scala
//----------------------分区----------------------
val part=sc.textFile("/opt/spark/README.md")
//part: org.apache.spark.rdd.RDD[String] = /opt/spark/README.md MapPartitionsRDD[1] at textFile at <console>:24
part.partitions.size
//res2: Int = 2
val part1=sc.textFile("/opt/spark/README.md",6)
//part1: org.apache.spark.rdd.RDD[String] = /opt/spark/README.md MapPartitionsRDD[3] at textFile at <console>:24
part1.partitions.size
//res3: Int = 6
val rdd=sc.textFile("/opt/spark/README.md")
//rdd: org.apache.spark.rdd.RDD[String] = /opt/spark/README.md MapPartitionsRDD[5] at textFile at <console>:24
val wordmap=rdd.flatMap(_.split(" ")).map(x=>(x,1))
//org.apache.spark.rdd.RDD[(String, Int)] = MapPartitionsRDD[7] at map at <console>:25
println(wordmap)
//MapPartitionsRDD[7] at map at <console>:25
 wordmap.dependencies.foreach {
     | dep =>
     | println("dependency type:" +dep.getClass)
     | println("dependency RDD:"+dep.rdd)
     | println("dependency partitions:"+dep.rdd.partitions)
     | println("dependency partitions size:"+dep.rdd.partitions.length)
     | }
//dependency type:class org.apache.spark.OneToOneDependency
//dependency RDD:MapPartitionsRDD[6] at flatMap at <console>:25
//dependency partitions:[Lorg.apache.spark.Partition;@37e5111b
//dependency partitions size:2
//依赖关系是一对一，窄依赖
//哪些是窄依赖和宽依赖
val wordreduce=wordmap.reduceByKey(_+_)
//wordreduce: org.apache.spark.rdd.RDD[(String, Int)] = ShuffledRDD[8] at reduceByKey at <console>:25
println(wordreduce)
//ShuffledRDD[8] at reduceByKey at <console>:25
wordreduce.dependencies.foreach { dep=>
     | println("dependency type:" +dep.getClass)
     | println("dependency RDD:"+dep.rdd)
     | println("dependency partitions:"+dep.rdd.partitions)
     | println("dependency partitions size:"+dep.rdd.partitions.length)
     | println("dependency partitions size:"+dep.rdd.partitions.size)
     | }
//dependency type:class org.apache.spark.ShuffleDependency
//dependency RDD:MapPartitionsRDD[7] at map at <console>:25
//dependency partitions:[Lorg.apache.spark.Partition;@37e5111b
//dependency partitions size:2
//dependency partitions size:2
//分区计算mapPartitions()

val a=sc.parallelize(1 to 9 ,3)
//a: org.apache.spark.rdd.RDD[Int] = ParallelCollectionRDD[9] at parallelize at <console>:24
val a=sc.parallelize(1 to 9 ,3)
//a: org.apache.spark.rdd.RDD[Int] = ParallelCollectionRDD[9] at parallelize at <console>:24

def iterfunc [T] (iter:Iterator[T]) : Iterator[(T,T)] = {
     |     var res=List[(T,T)]()
     |     var pre=iter.next
     | while(iter.hasNext){
     |        val cur = iter.next
     |        res ::=(pre,cur)
     | pre = cur
     | }
     |   res.iterator
     | }
//iterfunc: [T](iter: Iterator[T])Iterator[(T, T)]
a.mapPartitions(iterfunc).collect
//res10: Array[(Int, Int)] = Array((2,3), (1,2), (5,6), (4,5), (8,9), (7,8)) 
```

## 1.2分区函数(Partitioner)

spark默认提供两种划分器: 哈希分区划分器(HashPartitioner)和范围分区划分器(RangePartitioner)

```scala
val part = sc.textFile("/opt/spark/README.md")
//part: org.apache.spark.rdd.RDD[String] = /opt/spark/README.md MapPartitionsRDD[1] at textFile at <console>:24
part.partitioner
//res0: Option[org.apache.spark.Partitioner] = None
val group_rdd = part.map(x=>(x,x)).groupByKey (new org.apache.spark.HashPartitioner(4))
//group_rdd: org.apache.spark.rdd.RDD[(String, Iterable[String])] = ShuffledRDD[3] at groupByKey at <console>:25

```

## 1.3创建操作

### 1.3.1并行化集合操作

```scala
//并行化集合操作
//默认的分区数量为4，因为spark-shell开启了三个worker节点
var rdd = sc.parallelize(1 to 10)
//rdd: org.apache.spark.rdd.RDD[Int] = ParallelCollectionRDD[4] at parallelize at <console>:24

rdd.collect
//res2: Array[Int] = Array(1, 2, 3, 4, 5, 6, 7, 8, 9, 10)                         

rdd.partitions.size
//res3: Int = 3
var rdd2 = sc.parallelize(1 to 10,4)
//rdd2: org.apache.spark.rdd.RDD[Int] = ParallelCollectionRDD[5] at parallelize at <console>:24

rdd2.collect
//res4: Array[Int] = Array(1, 2, 3, 4, 5, 6, 7, 8, 9, 10)
rdd2.partitions.size
//res6: Int = 4

//-----------------------------makeRDD-----------------------------
 val  collect = Seq((1 to 10,Seq("master","slave1")),(11 to 15,Seq("slave2","slave3")))
//collect: Seq[(scala.collection.immutable.Range.Inclusive, Seq[String])] = List((Range(1, 2, 3, 4, 5, 6, 7, 8, 9, 10),List(master, slave1)), (Range(11, 12, 13, 14, 15),List(slave2, slave3)))

var rdd_make = sc.makeRDD(collect)
//rdd_make: org.apache.spark.rdd.RDD[scala.collection.immutable.Range.Inclusive] = ParallelCollectionRDD[6] at makeRDD at <console>:26
//该RDD分区数，分区和首选位置分布一致
rdd_make.partitions.size
//res7: Int = 2

rdd_make.preferredLocations(rdd_make.partitions(0))
//res8: Seq[String] = List(master, slave1)

rdd_make.preferredLocations(rdd_make.partitions(1))
//res9: Seq[String] = List(slave2, slave3)

```

### 1.3.2外部存储创建操作

```scala
textFile
//sparklearning.txt的文件内容
soft kitty
warm kitty
little ball of fur
happy kitty
sleepy kitty
pur pur pur
//注意：此处的sparklearning.txt需要在三个节点上都有该文件，否则会报错
var text_rdd=sc.textFile("/home/spark/sparklearning.txt")
//text_rdd: org.apache.spark.rdd.RDD[String] = /home/spark/sparklearning.txt MapPartitionsRDD[8] at textFile at <console>:24

scala> text_rdd.count
// java.io.FileNotFoundException: File file:/home/spark/sparklearning.txt does not exist at org.apache.hadoop.fs.RawLocalFileSystem.deprecatedGetFileStatus(RawLocalFileSystem.java:611)

var hdfstextfile_rdd=sc.textFile("hdfs://192.168.145.101:8020/sparklearning.txt")
//hdfstextfile_rdd: org.apache.spark.rdd.RDD[String] = hdfs://192.168.145.101:8020/sparklearning.txt MapPartitionsRDD[12] at textFile at <console>:24

hdfstextfile_rdd.count
//res21: Long = 6
```

## 1.4转换操作

### 基础转换

#### 1.4.1 map|distinct|flatMap

```scala

var data=sc.textFile("hdfs://192.168.145.104:8020/sparklearning.txt")
//data: org.apache.spark.rdd.RDD[String] = hdfs://192.168.145.104:8020/sparklearning.txt MapPartitionsRDD[4] at textFile at <console>:24

data.map(line => line.split("\\s+")).collect
//res1: Array[Array[String]] = Array(Array(soft, kitty), Array(warm, kitty), Array(little, ball, of, fur), Array(happy, kitty), Array(sleepy, kitty), Array(pur, pur, pur))

data.flatMap(line => line.split("\\s+")).collect
//res2: Array[String] = Array(soft, kitty, warm, kitty, little, ball, of, fur, happy, kitty, sleepy, kitty, pur, pur, pur)

data.flatMap(line => line.split("\\s+")).distinct.collect
//res3: Array[String] = Array(sleepy, soft, little, happy, pur, fur, warm, of, ball, kitty


```

#### 1.4.2 colaesce|repartition[shuffle为true]

```scala
//重新分区
var rdd1=data.coalesce(1)
//rdd1: org.apache.spark.rdd.RDD[String] = CoalescedRDD[11] at coalesce at <console>:25
rdd1.partitions.size
//res7: Int = 1

//重新分区的数目小于原分区数目，可以正常进行；如果重新分区的数目大于原来的分区数，那么必须指定shuffle参数为true，否则分区数不变
var rdd1=data.coalesce(4)
//rdd1: org.apache.spark.rdd.RDD[String] = CoalescedRDD[12] at coalesce at <console>:25
rdd1.partitions.size
//res8: Int = 2

var rdd1=data.coalesce(4,true)
//rdd1: org.apache.spark.rdd.RDD[String] = MapPartitionsRDD[16] at coalesce at <console>:25

rdd1.partitions.size
//res9: Int = 4

```

#### 1.4.3 randomSplit |glom

```scala
//---------------------randomSplit    glom()---------------------
var rdd = sc.makeRDD(1 to 10 ,10)
//rdd: org.apache.spark.rdd.RDD[Int] = ParallelCollectionRDD[17] at makeRDD at <console>:24
rdd.partitions.size
//res11: Int = 10
//权重高的RDD划分到的几率就大一些
var splitRDD = rdd.randomSplit(Array(1.0,2.0,3.0,4.0))
//splitRDD: Array[org.apache.spark.rdd.RDD[Int]] = Array(MapPartitionsRDD[18] at randomSplit at <console>:25, MapPartitionsRDD[19] at randomSplit at <console>:25, MapPartitionsRDD[20] at random
//Split at <console>:25, MapPartitionsRDD[21] at randomSplit at <console>:25)
splitRDD.size
//res12: Int = 4

splitRDD(0).collect
//res13: Array[Int] = Array(8)
splitRDD(1).collect
//res14: Array[Int] = Array(2, 6, 10)
splitRDD(2).collect
//res15: Array[Int] = Array(4, 7, 9)
splitRDD(3).collect
//res16: Array[Int] = Array(1, 3, 5)


var rdd=sc.makeRDD(1 to 10,3)
//rdd: org.apache.spark.rdd.RDD[Int] = ParallelCollectionRDD[22] at makeRDD at <console>:24

rdd.glom().collect
//res17: Array[Array[Int]] = Array(Array(1, 2, 3), Array(4, 5, 6), Array(7, 8, 9, 10))
```

#### 1.4.4 union | intersection

```scala
var rdd1 = sc.makeRDD(1 to 2,1)
//rdd1: org.apache.spark.rdd.RDD[Int] = ParallelCollectionRDD[24] at makeRDD at <console>:24

var rdd2 = sc.makeRDD(2 to 3,1)
//rdd2: org.apache.spark.rdd.RDD[Int] = ParallelCollectionRDD[25] at makeRDD at <console>:24

rdd1.union(rdd2).collect
//res18: Array[Int] = Array(1, 2, 2, 3)

rdd1.intersection(rdd2).collect
//res19: Array[Int] = Array(2)
```

#### 1.4.5 mapPartitions ||mapPartitionsWithIndex

```scala
var rdd1 = sc.makeRDD(1 to 5,2)
//rdd1: org.apache.spark.rdd.RDD[Int] = ParallelCollectionRDD[33] at makeRDD at <console>:24

var rdd3=rdd1.mapPartitions{ x =>{
     | var result =List[Int]()
     |     var i=0
     |     while(x.hasNext){
     |      i +=x.next()
     |     }
     |     result.::(i).iterator
     | }}
//rdd3: org.apache.spark.rdd.RDD[Int] = MapPartitionsRDD[34] at mapPartitions at <console>:25

rdd3.collect
//res20: Array[Int] = Array(3, 12)

rdd3.partitions.size
//res21: Int = 2


var rdd1 = sc.makeRDD(1 to 5,2)
//rdd1: org.apache.spark.rdd.RDD[Int] = ParallelCollectionRDD[0] at makeRDD at <console>:24

var rdd3=rdd1.mapPartitionsWithIndex{
     | (x,iter)=>{
     | var result =List[String]()
     | var i=0
     | while(iter.hasNext){
     | i+=iter.next()
     | }
     | result.::(x + "|"+i).iterator
     | }}
//rdd3: org.apache.spark.rdd.RDD[String] = MapPartitionsRDD[1] at mapPartitionsWithIndex at <console>:25
rdd3.collect
//res1: Array[String] = Array(0|3, 1|12)

```

#### 1.4.5 zip| zipWithIndex |

```scala
var rdd1 = sc.makeRDD(1 to 5,2)
//rdd1: org.apache.spark.rdd.RDD[Int] = ParallelCollectionRDD[2] at makeRDD at <console>:24
//两个rdd必须是对应的
var rdd2 = sc.makeRDD(Seq("A","B","C","D","E"),2)
//rdd2: org.apache.spark.rdd.RDD[String] = ParallelCollectionRDD[3] at makeRDD at <console>:24

rdd1.zip(rdd2).collect
//res2: Array[(Int, String)] = Array((1,A), (2,B), (3,C), (4,D), (5,E)) 
//不对应是报错
var rdd2 = sc.makeRDD(Seq("A","B","C","D","E"),3)
//rdd2: org.apache.spark.rdd.RDD[String] = ParallelCollectionRDD[5] at makeRDD at <console>:24

rdd1.zip(rdd2).collect
//java.lang.IllegalArgumentException: Can't zip RDDs with unequal numbers of partitions: List(2, 3)

```

### 键值转换

## 1.5控制操作





# Wodcount案例

```shell
cd /opt/spark/bin
./spark-shell --master spark://datanode1:7077 --executor-memory 1024m --driver-memory 1024m
```

```scala
sc.textFile("/opt/spark/README.md").flatMap(_.split(" ")).map(x=>(x,1)).reduceByKey(_+_).map(x=>(x._2,x._1)).sortByKey(false).map(x=>(x._2,x._1)).take(10)

##查看web界面
```



# 年降水

数据集下载

<https://cdiac.ess-dive.lbl.gov/ftp/ndp040/>



```shell
hadoop fs -put f20674.dat /ussr
./spark-shell --master spark://datanode1:7077 --executor-memory 1536m --driver-memory 1024m
##
hadoop dfs -chown spark:spark /ussr
```



```scala
val pre_data=sc.textFile("hdfs://192.168.145.101:8020/ussr/f20674.dat")

val fields=pre_data.map(line=>line.trim().replace("   "," ").replace("  "," ")split(" ")).filter(_.length==15).filter(_(13)!="9").filter(_(11)!="999.9")

val pre_ann=fields.map(fields => (fields(1),fields(11).toDouble)).groupByKey().map{ x=>(x._1,x._2.reduce(_+_))}

val pre_sort=pre_ann.map(x=>(x._2*365,x._1)).sortByKey(false).map(x=>(x._2,x._1))

pre_sort.saveAsTextFile("hdfs://192.168.145.101:8020/ussr/out_pre")
##最后的/ussr/out_pre权限是spark
```



```shell
##取出文件
hadoop dfs -getmerge /ussr/out_pre/ /home/master/ussr.txt
```





import org.apache.spark.mllib.linalg._

import org.apache.spark.mllib.regression._

val rawData = sc.textFile("hdfs:///usr/ds/covtype.data")



val data =rawData.map{line=>
     |   val values=line.split(',').map(_.toDouble)
     |   val featureVector = values.last - 1
     |   val label = values.last - 1
     |   LabeledPoint(label,featureVector )
     | }







# sougou日志分析

```shell
cd /opt/spark/bin
./spark-shell --master spark://datanode1:7077 --executor-memory 1024m --driver-memory 1024m
```

