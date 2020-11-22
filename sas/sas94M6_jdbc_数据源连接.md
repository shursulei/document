## 技术问题

### 1、与各数据库连接的问题

#### 1.1 oracle

1. libaname的新建表存在问题
2. 特殊字段如date、clob、timestamp等特殊类型，采用proc的方式查询出错，现阶段给与的方案是使用sas的data方式进行转换。     

#### 1.2 mppdb

1. access to jdbc测试华为mppdb的中文编码问题，对mppdb数据库的读和写的操作均存在问题。
2. 性能对比测试，经测试使用131M的数据，分别使用access to jdbc和access to postgresql从sas导入到mppdb数据库，但是结果access to jdbc的性能低于postgresql
3. bulkload的还未测试

#### 1.3  华为hadoop

access to jdbc连接华为hadoop，还没有连通。还未测试



# 结论

1. jdbc连接华为hadoop，连通性还没有解决，功能测试还未开始。

2. jdbc对数据库的连接，oralce存在libname创建表的问题，特殊字段的类型需要转换；mppdb存在中文编码的问题；其他的测试点还未测试












## ACCESS TO JDBC测试工作

### 1.多个用户对ACCESS to jdbc的访问的性能压力测试。

### 2.各数据库的测试

#### 2.1Oracle

​	1.增删改查的测试(包括libname和proc的两种方式)

​        2.带有中文字符等特殊的测试点

​        3.大数据数据性能的读和写的测试。

​	4.与Access to oracle的对比，包括以上的测试。

#### 2.2Mysql

​	1.增删改查的测试(包括libname和proc的两种方式)

​        2.带有中文字符等特殊的测试点

​        3.大数据数据性能的读和写的测试。

#### 2.3Mppdb

​	1.增删改查的测试(包括libname和proc的两种方式)

​        2.带有中文字符等特殊的测试点

​        3.大数据量数据性能的读和写的测试。

​        4.bulkload的测试(lvs的性能测试)

​	5.与Access to pg的对比，包括以上的测试。

#### 2.4Hadoop

​	1.增删改查的测试(包括libname和proc的两种方式)

​        2.带有中文字符等特殊的测试点

​        3.大数据量数据性能的读和写的测试.

​	4.与Access to hadoop的对比，包括以上的测试。



## jdbc目前做已完成工作

### Oracle

access to jdbc与oracle的连通性测试完成。

access to oracel与oracle的连通性测试完成。

### Mysql

access to jdbc与mysql的连通性测试完成。

### Mppdb

1.access to jdbc与mppdb的连通性测试完成。

2.增删改查的测试完成，包括(libname和proc的测试已经完成)。

(但是工行的mppdb的库写入中文字符报错)

3.access to postgresql连通测试完成。

### Hadoop

1.access to jdbc与hadoop的连通性测试存在问题。

 当前工行hadoop使用的华为的hadoopC70的版本，按照之前viya和sas94M1的配置方式进行测试。与现在版本的测试存在问题。

2.access to hadoop 与hadoop的连通性测试存在问题。



先测试proc.

码值问题

性能比较测试



libname/bulkload





hadoop的回写测试：

原生态回写;

中间转换。

性能比较。

wangtao999@huawei