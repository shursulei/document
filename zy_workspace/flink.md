# flink单机安装



flink-conf.yaml的配置文件

```
taskmanager.numberOfTaskSlots: 10
parallelism.default: 5
io.tmp.dirs: /data/flink-1.11.1/data/tmp/tmp1:/data/flink-1.11.1/data/tmp/tmp2:/data/flink-1.11.1/data/tmp/tmp3
```

mkdir -p /data/flink-1.11.1/data/tmp/{tmp1,tmp2,tmp3}

mkdir -p /data/flink-1.11.1/data/history

mv ~/.flink-sql-history /data/flink-1.11.1/data/history/flink-sql-history

启动

```

```



# Catalog 

接口Catalog

# flink-sql

部署环境(单机)

kafka:2.3.0

java:1.8

flink:1.11.1

启动kafka

```
./zookeeper-server-start.sh ../config/zookeeper.properties &
./kafka-server-start.sh ../config/server.properties &
```

数据插入kafka

```shell
./kafka-topics.sh --create --zookeeper localhost:2181 --replication-factor 1 --partitions 1 --topic test

./kafka-console-producer.sh --broker-list localhost:9092 --topic test


./kafka-topics.sh --zookeeper 127.0.0.1:2181 -list topic
./kafka-console-consumer.sh --bootstrap-server  localhost:9092 --topic test --from-beginning


./kafka-topics.sh --delete --topic test --zookeeper 127.0.0.1:2181

   # {"id": "10000001","t_order_id": "O181001RJN6N0525VC0G0","patient_id": "46266143","gmt_create": "2020-06-10T12:12:43Z","gmt_modify": "2020-06-10T12:12:43Z"}
    #注意事件的数据格式:2020-06-10T12:12:43Z
    #T表示分隔符，Z表示的是UTC。UTC：世界标准时间，在标准时间上加上8小时，即东八区时间，也就是北京时间。
```

启动flink

```
./start-cluster.sh
```

注册kafka消息为flink sql 的动态表

配置kafka-connect-flink.yml

```yaml
#==============================================================================
# Table Sources
#==============================================================================

# Define table sources and sinks here.

tables: # empty list
# A typical table source definition looks like:
 - name: prescription
   type: source
   update-mode: append
   connector: 
      property-version: 1
      type: kafka
      version: universal
      topic: test
      startup-mode: earliest-offset
      properties:
      - key: zookeeper.connect
        value: localhost:2181
      - key: bootstrap.servers
        value: localhost:9092
      - key: group.id
        value: test-consumer-group
   format: 
      property-version: 1
      type: json
      schema: "ROW( id LONG, t_order_id VARCHAR, patient_id LONG,  gmt_create TIMESTAMP,gmt_modify TIMESTAMP)"
   schema: 
      - name: id
        type: LONG
      - name: t_order_id
        type: VARCHAR
      - name: patient_id
        type: LONG
      - name: gmt_create
        type: TIMESTAMP
      - name: gmt_modify
        type: TIMESTAMP
        rowtime:
          timestamps:
            type: "from-field"
            from: "gmt_create"
          watermarks:
            type: "periodic-bounded"
            delay: "60000"

#==============================================================================
# User-defined functions
#==============================================================================

# Define scalar, aggregate, or table functions here.

functions: [] # empty list
# A typical function definition looks like:
# - name: ...
#   from: class
#   class: ...
#   constructor: ...

#==============================================================================
# Execution properties
#==============================================================================

# Execution properties allow for changing the behavior of a table program.

execution:
  # 'batch' or 'streaming' execution
  type: streaming
  # allow 'event-time' or only 'processing-time' in sources
  time-characteristic: event-time
  # interval in ms for emitting periodic watermarks
  periodic-watermarks-interval: 200
  # 'changelog' or 'table' presentation of results
  result-mode: table
  # maximum number of maintained rows in 'table' presentation of results
  max-table-result-rows: 1000000
  # parallelism of the program
  parallelism: 1
  # maximum parallelism
  max-parallelism: 128
  # minimum idle state retention in ms
  min-idle-state-retention: 3600000
  # maximum idle state retention in ms
  max-idle-state-retention: 7200000

#==============================================================================
# Deployment properties
#==============================================================================

# Deployment properties allow for describing the cluster to which table
# programs are submitted to.

deployment:
  # general cluster communication timeout in ms
  response-timeout: 5000
  # (optional) address from cluster to gateway
  gateway-address: ""
  # (optional) port from cluster to gateway
  gateway-port: 0
```

提前下载jar包

![image-20200909145459765](D:\software\typora\zy_workspace\flink.assets\image-20200909145459765.png)

启动 flink sql client

```
./sql-client.sh embedded  -d ../conf/kafka-connect-flink.yml -l ../sqllib/
```

运行sql语句:

```sql
SET execution.result-mode=table; --内存模式
```



# pyflink

Anaconda3安装

配置环境变量

```
export PATH=/opt/anaconda3/bin:$PATH
```



```shell
conda install pyarrow -c conda-forge
pip install pyflink
pip install apache-flink -i https://mirrors.aliyun.com/pypi/simple/
```





# flink的CDC

flink-json的下载地址

https://mvnrepository.com/artifact/org.apache.flink/flink-json/1.11.0

简介

Flink CDC Connector 是ApacheFlink的一组数据源连接器，使用**变化数据捕获change data capture (CDC)）**从不同的数据库中提取变更数据。Flink CDC连接器将Debezium集成为引擎来捕获数据变更。因此，它可以充分利用Debezium的功能。

特点

- 支持读取数据库快照，并且能够持续读取数据库的变更日志，即使发生故障，也支持**exactly-once** 的处理语义
- 对于DataStream API的CDC connector，用户无需部署Debezium和Kafka，即可在单个作业中使用多个数据库和表上的变更数据。
- 对于Table/SQL API 的CDC connector，用户可以使用SQL DDL创建CDC数据源，来监视单个表上的数据变更。

使用场景

- 数据库之间的增量数据同步
- 审计日志
- 数据库之上的实时物化视图
- 基于CDC的维表join

![image-20200911172310415](D:\software\typora\zy_workspace\flink.assets\image-20200911172310415.png)



flink sql client 连接mysql出现select 结果一直在等待

![image-20200911183733024](D:\software\typora\zy_workspace\flink.assets\image-20200911183733024.png)



# 构建实时数仓

```
https://www.jianshu.com/p/8fecaf1d0651
```

![image-20200909174704448](D:\software\typora\zy_workspace\flink.assets\image-20200909174704448.png)

![image-20200909174835963](D:\software\typora\zy_workspace\flink.assets\image-20200909174835963.png)



# Zeppelin

```shell
修改zeppelin-site.xml.template*
cp zeppelin-site.xml.template zeppelin-site.xml
修改端口
./zeppelin-daemon.sh start

netstat |grep 9999
curl 127.0.0.1:9999
```





# flink趟过的坑

1、flink sql 消费kafka失败

```markdown
Recovery is suppressed by NoRestartBackoffTimeStrategy 
```

该问题出现的原因可能是 字段中的时间格式



2、flink自己下载的包，放置新的存放地方，不建议放到lib下

先说解决办法，flink-table-planner-blink这个包不要编译到项目中去

```
cannot be cast to org.codehaus.commons.compiler.ICompilerFactory
```



stripMargin

```
scala之stripMargin和多行字符串的使用
```



pyflink代码

![pyflink](D:\software\typora\zy_workspace\flink.assets\pyflink.png)