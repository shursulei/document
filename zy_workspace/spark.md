# spark版本

3.0.0

# jupytenotebook结合pyspark

编辑/etc/profile,source /etc/profile

```shell
export PYSPARK_DRIVER_PYTHON=jupyter-notebook 
export PYSPARK_DRIVER_PYTHON_OPTS=" --ip=0.0.0.0 --port=8888 --allow-root"
```



# 单机模式安装



# pycharm 连接远端spark 进行调试



![image-20200901194040933](spark.assets\image-20200901194040933.png)



![image-20200901194057760](spark.assets\image-20200901194057760.png)

​	在这之前选择

![image-20200901194223391](spark.assets\image-20200901194223391.png)

上传



![image-20200901194406565](spark.assets\image-20200901194406565.png)



配置环境变量

![image-20200901194550881](spark.assets\image-20200901194550881.png)

```
PYSPARK_HOME=/opt/anaconda3/bin/python3.8;PYSPARK_DRIVER_HOME=/opt/anaconda3/bin/python3.8;JAVA_HOME=/opt/jdk1.8.0_60/;SPARK_HOME=/opt/spark-3.0.0-bin-hadoop2.7
```

配置python_interpreter

右键选择添加ssh interpreter

![image-20200901194752171](spark.assets\image-20200901194752171.png)



测试调试

![image-20200901194859202](spark.assets\image-20200901194859202.png)



# spark读取mysql的大数据过大



主要的解决步骤和想法是:

Spark的读取性能问题，利用分区的机制解决

```HTML
https://www.percona.com/blog/2016/08/17/apache-spark-makes-slow-mysql-queries-10x-faster/
https://blog.csdn.net/u013405116/article/details/88535651?utm_medium=distribute.pc_aggpage_search_result.none-task-blog-2~all~first_rank_v2~rank_v25-3-88535651.nonecase&utm_term=spark%20%E5%BE%88%E6%85%A2%20%E8%AF%BBmysql
https://www.percona.com/blog/2016/01/07/apache-spark-with-air-ontime-performance-data/
```

只要是query 这张大table都会出现OOM，重试几次甚至出现executor heartbeat timeout。





# Spark埋过的坑

scala的版本不对

```
java.lang.NoSuchMethodError: scala.Product.$init$(Lscala/Product;)V
```

idea的

引入的包不对，缺少相对应的包

解决idea编写spark程序时，import org.apache.spark报错