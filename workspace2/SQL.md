# MYSQL语句优化

```sql
 CREATE DATABASE IF NOT EXISTS test_db_char DEFAULT CHARACTER SET utf8 DEFAULT COLLATE utf8_chinese_ci;
 
 CREATE TABLE IF NOT EXISTS `runoob_tbl`(
   `runoob_id` INT UNSIGNED AUTO_INCREMENT,
   `runoob_title` VARCHAR(100) NOT NULL,
   `runoob_author` VARCHAR(40) NOT NULL,
   `submission_date` DATE,
   PRIMARY KEY ( `runoob_id` )
)ENGINE=InnoDB DEFAULT CHARSET=utf8;
INSERT INTO runoob_tbl (runoob_title, runoob_author, submission_date) VALUES ("学习 PHP", "菜鸟教程", NOW());
INSERT INTO runoob_tbl (runoob_title, runoob_author, submission_date) VALUES ("学习 MySQL", "菜鸟教程", NOW());
INSERT INTO runoob_tbl (runoob_title, runoob_author, submission_date) VALUES ("JAVA 教程", "RUNOOB.COM", '2016-05-06');
```





# Hive

## hive表分类

管理表、外部表、分区表{管理分区表、外部分区表}

### 分区表和分桶

```sql
#创建分区表
CREATE TABLE page_view(viewtime int,userid bigint,page_url string,ip string comment 'ip  address of the user') comment 'this is the page view table' partitioned by (dt string, country string) row format delimited fields terminated by '\001' stored as sequencefile;
#创建分桶
CREATE TABLE page_view2(viewtime int,userid bigint,page_url string,ip string comment 'ip  address of the user') comment 'this is the page view table' partitioned by (dt string, country string) CLUSTERED BY(userid) INTO 10 BUCKETS row format delimited fields terminated by '\001' stored as sequencefile;
describe formatted page_view;

insert into page_view partition (dt='2020-03-14',country='china')values(2,7878787979,'https://www.baidu.com','192.168.145.98');
insert into page_view partition (dt='2020-03-14',country='china')values(2,7878787979,'https://www.baidu.com','192.168.145.98');
insert into page_view partition (dt='2020-03-17',country='english')values(2,7878787979,'https://www.baidu.com','192.168.145.98');
insert into page_view partition (dt='2020-03-15',country='uk')values(2,7878787979,'https://www.baidu.com','192.168.145.98');
insert into page_view partition (dt='2020-03-16',country='usa')values(2,7878787979,'https://www.baidu.com','192.168.145.98');

select * from page_view where dt="2020-03-14";
explain select * from page_view; /*全盘扫描*/
explain select * from page_view where dt="2020-03-14";
explain select * from page_view where country="china";

set hive.mapred.mode=strict;/*如果数据量过大，会触发一个巨大的mapred任务*/
select * from page_view;
/**
FAILED: SemanticException [Error 10041]: No partition predicate found for Alias "page_view" Table "page_view"
*/
set hive.mapred.mode=nostrict;
```

创建分区表时，普通字段和分区字段不能重名。

```sql
CREATE external TABLE logs(
platform string,
createtime string,
channel string,
product string,
userid string,
content map<string,string>) 
partitioned by (dt int)  row format delimited fields terminated by '\t'
location '';
```

```shell
dt=$(date -d last-day +%Y%m%d)
hadoop fs -mkdir -p /logs/$dt
```

动态分区插入

```sql
/*非分区表装载数据*/
drop table if exists t1;
create table t1(name string,city string,st string) row format delimited fields terminated by ',';
load data local inpath '/root/test' into table t1;

create external table t2(name string) partitioned by (country string,state string);
set hive.exec.dynamic.partition=true;
set hive.exec.dynamic.partition.mode=nostrict;
set hive.exec.dynamic.partitions.pernode=1000;
insert into table t2 partition (country,state) select name,city,st from t1;
```



### 数据仓库/ETL/工作流 案例

#### mysql源表

```sql
--建立数据库表
drop database if exists source;
create database source;
use source;
--建立客户表
create table customer(
    customer_number int not null auto_increment primary key comment '客户编号，主键',
    customer_name varchar(50) comment '客户名称',
    customer_street_address varchar(50) comment '客户地址',
    customer_zip_code int comment '邮编',
    customer_city varchar(30) comment '所住城市',
    customer_state varchar(2) comment '所在省份'
)
--建立产品表
create table product(
    product_number int not null auto_increment primary key comment '产品编号，主键',
    product_name varchar(30) comment '产品名称',
    product_category varchar(30) comment '产品类型'
)
--建立销售订单表
create table sales_order(
    order_number int not null auto_increment primary key comment '订单号，主键',
    customer_number int comment '客户编号',
    product_code int comment '产品编码',
    order_date datetime comment '登记日期',
    order_amount decimal(10,2) comment '销售金额',
    order_quantity int comment 'order_quantity',
    foreign key (customer_number) reference on delete cascade on update cascade,
    foreign key (product_code) reference on delete cascade on update cascade
)
```

测试数据

```sql
use source;
--生成客户表测试数据
```



```sql
/*#mysql增加列,mysql支持after增加列的方式*/
use source;
alter table customer add shipping_address varchar(50) after customer_state,
add shipping_zip_code int after shipping_address,
add shipping_city varchar(30) after shipping_zip_code,
add shipping_state varchar(2) after shipping_city;
alter table sales_order add order_quantity int after order_amount;
/*hive修改，增加列*/
use rds;
alter table customer add cloumns (
shipping_address varchar(50) comment 'shipping_address',
shipping_zip_code int comment 'shipping_zip_code',
shipping_city varchar(30) comment 'shipping_city',
shipping_state varchar(2) comment 'shipping_state'
);
alter table sales_order add cloumns (order_quantity int comment 'order_quantity');
/*HiveQL修改DW数据源*/
use dw;
--修改客户维度表
--源表名作为备份表
alter table customer_dim rename to customer_dim_old;
--建立新表
create table customer_dim(
	customer_sk int comment 'surrogate key',
    customer_number int comment 'number',
    customer_name varchar(50) comment 'name',
    customer_street_address varchar(50) comment 'address',
    customer_zip_code int comment 'zipcode',
    customer_city varchar(30) comment 'city',
    customer_state varchar(2) comment 'state',
	shipping_address varchar(50) comment 'shipping_address',
	shipping_zip_code int comment 'shipping_zip_code',
	shipping_city varchar(30) comment 'shipping_city',
	shipping_state varchar(2) comment 'shipping_state',
    version int comment 'version',
    effective_date date comment 'effective date',
    expiry_date date comment 'expiry date'
)clustered by (customer sk) into 8 buckets
stored as orc tblproperties ('transactional'='true');
--导入备份表数据
insert into customer_dim select customer_sk,customer_number,customer_name,customer_street_address,customer_zip_code,customer_city,customer_state,null,null,null,null,version,effective_date,expiry_date from customer_dim_old;
--删除备份表
drop table customer_dim_old;

--修改销售订单事实表
alter table sales_old_fact rename to sales_old_fact_old;
create table sales_old_fact (
	order_sk int comment 'order surrogate key',
    customer_sk int comment 'customer surrogate key',
    product_sk int comment 'product surrogate key',
    order_date_sk int comment 'date surrogate key',
    order_amount decimal(10,2) comment 'order amount',
    order_quantity int comment 'order_quantity'
)clustered by (customer sk) into 8 buckets
stored as orc tblproperties ('transactional'='true');
insert into sales_old_fact select *,null from sales_old_fact_old;
drop table sales_old_fact_old;
```



```shell
last_value=`sqoop job --show myjob_incremental_import --meta-connect jdbc:hsqldb:hsql://cdh2:16000/sqoop | grep incremental.last.value | awk '{print $3}'`
sqoop job --delete  myjob_incremental_import --meta-connect jdbc:hsqldb:hsql://cdh2:16000/sqoop
sqoop job\
--meta-connect jdbc:hsqldb:hsql://cdh2:16000/sqoop \
--create  myjob_incremental_import \
--import --connect "jdbc:mysql://cdh1:3306/source?useSSL=false&user=root&password=mypassword"\
--table sales_order \
--columns "order_number,customer_number,product_code,order_date,entry_date,order_amount,order_quantity"\
--hive-import \
--hive-table rds.sales_order \
--incremental append \
--check-column order_number \
--last-value $last_value

```



```sql
/*修改定期装载regular_etl.sql文件,hivevar是传递的变量*/
update customer_dim set expir_date=${hivevar:pre_date} where customer_dim.customer_sk in (
select a.customer_sk from(select customer_sk,customer_number,customer_street_address,customer_zip_code,customer_city,customer_state,shipping_address,customer_zip_code,shipping_city,shipping_state from customer_dim where expiry_date=${hivevar:max_date}) a left join rds.customer b on a.customer_number=b.customer_number where b.customer_number is null or (
!(a.customer_street_address<=>b.customer_street_address) or !(a.shipping_address<=>b.shipping_address)));
```



```sql
mysql中 insert …select …带来的问题


当使用insert...select...进行记录的插入时，如果select的表是innodb类型的，不论insert的表是什么类型的表，都会对select的表的纪录进行锁定。


对于那些从oracle迁移过来的应用，需要特别的注意，因为oracle并不存在类似的问题，所以在oracle的应用中insert...select...操作非常的常见。例如：有时候会对比较多的纪录进行统计分析，然后将统计的中间结果插入到另外一个表，这样的操作因为进行的非常少，所以可能并没有设置相应的索引。如果迁移到mysql数据库后不进行相应的调整，那么在进行这个操作期间，对需要select的表实际上是进行的全表扫描导致的所有记录的锁定，将会对应用的其他操作造成非常严重的影响。


究其主要原因，是因为mysql在实现复制的机制时和oracle是不同的，如果不进行select表的锁定，则可能造成从数据库在恢复期间插入结果集的不同，造成主从数据的不一致。如果不采用主从复制，关闭binlog并不能避免对select纪录的锁定，某些文档中提到可以通过设置innodb_locks_unsafe_for_binlog来避免这个现象，当这个参数设置为true的时候，将不会对select的结果集加锁，但是这样的设置将可能带来非常严重的隐患。如果使用这个binlog进行从数据库的恢复，或者进行主数据库的灾难恢复，都将可能和主数据库的执行效果不同。


因此，我们并不推荐通过设置这个参数来避免insert...select...导致的锁，如果需要进行可能会扫描大量数据的insert...select操作，我们推荐使用select...into outfile和load data infile的组合来实现，这样是不会对纪录进行锁定的,但是效率会下降 

```

