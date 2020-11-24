# Mysql架构

1、基础架构图

![image-20200812172737783](mysql.assets\image-20200812172737783.png)

2、mysql的线程池



3、引擎版本

```sql
show table status like 'sys_accont' 
```



## mysql镜像源

```php+HTML
http://mirrors.163.com/mysql/Downloads/MySQL-8.0/
```



## 日志log

binary log

```mysql
show binary logs;
show master logs;
show variables like 'log_%';
SHOW VARIABLES LIKE "general_log%";
SET GLOBAL general_log = 'ON';
show variables like '%expire_logs_days%'

show binary logs;
show binlog events;
show binlog events in 'mysql-bin.000087';
-- 查看表的更新时间和创建时间
show table status like 'tmp_member_advisory3';
```

## 查看进程信息

```sql
show processlist;
select INFO from information_schema.processlist where user='sulei';
```

## 查看自增的id

```sql
SELECT AUTO_INCREMENT FROM information_schema.tables WHERE table_name="tableName";
```



## SQL语法用法

#### rand/hour

```sql
SELECT RAND( ), RAND( ), RAND( );
#MySQL RAND函数，可以调用该函数以产生0到1之间的随机数
#HOUR - 返回小时（0 - 23）
select HOUR(time)
DATE_FORMAT(date,format)
```



#### MySQL Order By Rand()效率

可以选择最大值和最小值的差集

```
SELECT * 
FROM `table` AS t1 JOIN (SELECT ROUND(RAND() * ((SELECT MAX(id) FROM `table`)-(SELECT MIN(id) FROM `table`))+(SELECT MIN(id) FROM `table`)) AS id) AS t2 
WHERE t1.id >= t2.id 
ORDER BY t1.id LIMIT 1;
```



#### 变量

```sql
select @minMid:=(select min(id) from medical.patient_advisory_info_20200810);
```

#### sql update的随机

```
update aaa set xx=(select yy from bbb order by rand() limit 1)
```



```sql
-- 从同一张表更新数据的时候出现如下的错误
-- You can't specify target table '表名' for update in FROM clause
CREATE DEFINER=`sulei`@`%` PROCEDURE `change_ad_doctor_id2`()
BEGIN
  DECLARE s int DEFAULT 0;
  DECLARE has_error int DEFAULT 0;
  
  DECLARE x_day int(10);
  DECLARE x_hour int(3);
  DECLARE x_doctor_id bigint(20);
  DECLARE x_change_num int(10);
  DECLARE x_id bigint(20);
  
  DECLARE runsql CURSOR FOR select d.t_day,d.t_hour,d.doctor_id,d.change_num from tmp_member_advisory_doctor4 d order by d.t_day,d.t_hour,d.seq,d.change_num;
  DECLARE CONTINUE HANDLER FOR NOT FOUND SET s=1;

  open runsql;
    fetch runsql into x_day,x_hour,x_doctor_id,x_change_num;
    while s<>1 do
      while x_change_num > 0 do
        BEGIN
          DECLARE EXIT HANDLER FOR NOT FOUND SET x_change_num = 0;
				UPDATE tmp_member_advisory4 a SET a.new_doctor_id = x_doctor_id,a.is_change = 1 WHERE
					a.id = (select p.id from (SELECT t.id FROM tmp_member_advisory4 t WHERE t.t_day = x_day AND t.t_hour = x_hour AND t.is_change = 0 ORDER BY t.seq LIMIT 1 ) as p);
          set x_change_num = x_change_num - 1;
        end;
      end while;
      fetch runsql into x_day,x_hour,x_doctor_id,x_change_num;
    end while;
  -- 关闭游标
  close runsql;
END
```



## select *  /select 1 /select clname

```txt
可以用来查询表中是否有符合条件的记录(比如select 1 from seckill where id = 1001;)，select 1一般用来当作条件使用，比如exists( select 1 from 表名)等。select 1的效率比select 列名和select*快，因为不用查字典表。
```

## MYSQL中的COLLATE

DBA必考

COLLATE会影响到ORDER BY语句的顺序，会影响到WHERE条件中大于小于号筛选出来的结果，会影响**DISTINCT**、**GROUP BY**、**HAVING**语句的查询结果。另外，mysql建索引的时候，如果索引列是字符类型，也会影响索引创建，只不过这种影响我们感知不到。总之，凡是涉及到字符类型比较或排序的地方，都会和COLLATE有关。

```sql
CREATE TABLE `tmp_member_advisory2` (
  `id` bigint(20) NOT NULL,
  `doctor_id` bigint(20) COMMENT '会员医生ID',
  `patient_id` bigint(20) COMMENT '患者ID',
  `pid` bigint(20) COMMENT '处方ID',
  `order_num`  varchar(50) CHARACTER SET utf8mb4 COLLATE utf8mb4_general_ci COMMENT '订单号',
  `gmt_create` timestamp NULL DEFAULT NULL COMMENT '分配比例',
	`distr_type` int(2) DEFAULT '0' COMMENT '分类 0 19年11月-20年3月的 会员问诊 1 20年4月开始的 会员问诊:',
  `t_day` int(10) DEFAULT '0' COMMENT '日期',
  `t_hour` int(3) DEFAULT '0' COMMENT '小时',
  -- `t_scheduling` int(3) DEFAULT '0' COMMENT '排班时段 1:7-12  2:13-19  0:20-24 0-6',
  `new_doctor_id` bigint(20) COMMENT '会员医生ID',
  `is_change` int(1) DEFAULT 0 COMMENT '是否更换了医生ID, 1 是 0 否',
  `seq` float(4,3) DEFAULT '0.0' COMMENT '随机排序',
  PRIMARY KEY (`id`),
  KEY `IDX_t_day` (`t_day`),
  KEY `IDX_is_change` (`is_change`)
) ENGINE=InnoDB COMMENT='新会员医生分配表';
```

COLLATE通常是和数据编码（CHARSET）相关的，一般来说每种CHARSET都有多种它所支持的COLLATE，并且每种CHARSET都指定一种COLLATE为默认值。例如Latin1编码的默认COLLATE为latin1_swedish_ci，GBK编码的默认COLLATE为gbk_chinese_ci，utf8mb4编码的默认值为utf8mb4_general_ci。

这里顺便讲个题外话，mysql中有utf8和utf8mb4两种编码，**在mysql中请大家忘记****utf8****，永远使用****utf8mb4**。这是mysql的一个遗留问题，mysql中的utf8最多只能支持3bytes长度的字符编码，对于一些需要占据4bytes的文字，mysql的utf8就不支持了，要使用utf8mb4才行



很多COLLATE都带有_ci字样，这是Case Insensitive的缩写，即大小写无关，也就是说"A"和"a"在排序和比较的时候是一视同仁的。selection * from table1 where field1="a"同样可以把field1为"A"的值选出来。与此同时，对于那些_cs后缀的COLLATE，则是Case Sensitive，即大小写敏感的。



### 查看到mysql所支持的所有COLLATE

```sql
show collation;
select * from information_schema.COLLATIONS;
#结论：推荐使用utf8mb4_unicode_ci，对于已经用了utf8mb4_general_ci的系统，也没有必要花时间改造。
```



### COLLATE设置级别及其优先级

设置COLLATE可以在示例级别、库级别、表级别、列级别、以及SQL指定。实例级别的COLLATE设置就是mysql配置文件或启动指令中的collation_connection系统变量。

库级别设置COLLATE的语句如下：

> CREATE DATABASE <db_name> DEFAULT CHARACTER SET utf8mb4 COLLATE utf8mb4_unicode_ci;

如果库级别没有设置CHARSET和COLLATE，则库级别默认的CHARSET和COLLATE使用实例级别的设置。在mysql8.0以下版本中，你如果什么都不修改，默认的CHARSET是Latin1，默认的COLLATE是latin1_swedish_ci。从mysql8.0开始，默认的CHARSET已经改为了utf8mb4，默认的COLLATE改为了utf8mb4_0900_ai_ci。

表级别的COLLATE设置，则是在CREATE TABLE的时候加上相关设置语句，例如：

> CREATE TABLE (
>
> ……
>
> ) ENGINE=InnoDB DEFAULT CHARSET=utf8mb4 COLLATE=utf8mb4_unicode_ci;

如果表级别没有设置CHARSET和COLLATE，则表级别会继承库级别的CHARSET与COLLATE。

列级别的设置，则在CREATE TABLE中声明列的时候指定，例如

> CREATE TABLE (
>
> `field1` VARCHAR（64） CHARACTER SET utf8mb4 COLLATE utf8mb4_general_ci NOT NULL DEFAULT '',
>
> ……
>
> ) ……



如果列级别没有设置CHARSET和COLATE，则列级别会继承表级别的CHARSET与COLLATE。

最后，你也可以在写SQL查询的时候显示声明COLLATE来覆盖任何库表列的COLLATE设置，不太常用，了解即可：

> SELECT DISTINCT field1 COLLATE utf8mb4_general_ci FROM table1;
>
> SELECT field1, field2 FROM table1 ORDER BY field1 COLLATE utf8mb4_unicode_ci;

如果全都显示设置了，那么优先级顺序是 SQL语句 > 列级别设置 > 表级别设置 > 库级别设置 > 实例级别设置。也就是说列上所指定的COLLATE可以覆盖表上指定的COLLATE，表上指定的COLLATE可以覆盖库级别的COLLATE。如果没有指定，则继承下一级的设置。即列上面没有指定COLLATE，则该列的COLLATE和表上设置的一样。

以上就是关于mysql的COLLATE相关知识。不过，在系统设计中，我们还是要尽量避免让系统严重依赖中文字段的排序结果，在mysql的查询中也应该尽量避免使用中文做查询条件。



## 查询表的索引

```sql
show index from tmp_member_advisory_doctor;
show keys from ih.prescription;
```

## 存储过程

查询库中的存储过程和存储函数

```mysql
show procedure status;
show function status;
```



```sql
DECLARE CONTINUE HANDLER FOR NOT FOUND set s=1;
#删除存储过程
DROP PROCEDURE productpricing ;
```



```
SHOW PROCEDURE STATUS useCursor()
```



## 游标

游标（cursor）是一个存储在MYSQL服务器上的数据库查询，它不是一条SELECT语句，而是被该语句检索出来的结果集。在存储了游标之后，应用程序可以根据需要滚动或浏览其中的数据。

```sql
DECLARE cursor-name CURSOR FOR SELECT ...;
OPEN cursor-name;
FETCH cursor-name INTO variable [, variable];
CLOSE cursor-name;
```

### demo

```sql
CREATE PROCEDURE curdemo()
BEGIN
DECLARE done INT DEFAULT 0;
DECLARE a CHAR(16);
DECLARE b,c INT;
DECLARE cur1 CURSOR FOR SELECT id,data FROM test.t1;
DECLARE cur2 CURSOR FOR SELECT i FROM test.t2;
DECLARE CONTINUE HANDLER FOR SQLSTATE '02000' SET done = 1;
OPEN cur1;
OPEN cur2;
REPEAT
FETCH cur1 INTO a, b;
FETCH cur2 INTO c;
IF NOT done THEN
IF b < c THEN
INSERT INTO test.t3 VALUES (a,b);
ELSE
INSERT INTO test.t3 VALUES (a,c);
END IF;
END IF;
UNTIL done END REPEAT;
CLOSE cur1;
CLOSE cur2;
END
```



## 触发器

**创建触发器**

​    创建触发器需要给出4条信息

​    1 唯一的触发器名； //保存每个数据库中的触发器名唯一

​    2 触发器关联的表；

​    3 触发器应该响应的活动（DELETE、INSERT或UPDATE）

​    4 触发器何时执行（处理前还是后,前是BEFORE 后是AFTER）

​    创建触发器用CREATE TRIGGER

​    CREATE TRIGGER newproduct AFTER INSERT ON products

​    FOR EACH ROW SELECT'Product added'

​    创建新触发器newproduct ，它将在INSERT语句成功执行后执行。这个触发器还镇定FOR EACH ROW,因此代码对每个插入的行执行。这个例子作用是文本对每个插入的行显示一次product added

​    FOR EACH ROW 针对每个行都有作用，避免了INSERT一次插入多条语句



触发器定义规则

触发器按每个表每个事件每次地定义，每个表每个事件每次只允许定义一个触发器，因此，每个表最多定义6个触发器（每条INSERT UPDATE 和DELETE的之前和之后）。单个触发器不能与多个事件或多个表关联，所以，如果你需要一个对INSERT 和UPDATE存储执行的触发器，则应该定义两个触发器

```sql
SELECT * FROM information_schema.`TRIGGERS`
```



## 事件

```sql
show variables like '%event_scheduler%';
-- 打开的方法：SET GLOBAL event_scheduler=1，关闭当然就是赋值为0了。


delimiter $$

-- SET GLOBAL event_scheduler = ON$$     
-- required for event to execute but not create  事件执行但不创建所需

-- event 后面是事件名称
-- DEFINER = { user | CURRENT_USER } 是定义人
create	/*[DEFINER = { user | CURRENT_USER }]*/	event `spring_boot_building`.`test`

on schedule
     /* uncomment the example below you want to use */
    /* 取消注释下面要使用的示例 */

    -- scheduleexample 1: run once
    -- 例子1：执行一次
    
        --  AT 'YYYY-MM-DD HH:MM.SS'/CURRENT_TIMESTAMP { + INTERVAL 1 [HOUR|MONTH|WEEK|DAY|MINUTE|...] }
        -- 使用 AT 关键字执行一次，使用时把上面注释放开即可

    -- scheduleexample 2: run at intervals forever after creation
    -- 例子2：每隔一段时间执行一次

        -- EVERY 1 [HOUR|MONTH|WEEK|DAY|MINUTE|...]
        -- 使用 EVERY 关键字执行一次，使用时把上面注释放开即可

    -- scheduleexample 3: specified start time, end time and interval for execution
    -- 例子3：指定开始时间、结束时间，在时间区间内每隔一段时间执行一次

        -- 使用 EVERY 关键字指定时间间隔
	   /*EVERY 1  [HOUR|MONTH|WEEK|DAY|MINUTE|...]

        -- 使用 STARTS 关键字指定开始时间
	   STARTS CURRENT_TIMESTAMP/'YYYY-MM-DD HH:MM.SS' { + INTERVAL 1[HOUR|MONTH|WEEK|DAY|MINUTE|...] }

        -- 使用 ENDS 关键字指定结束时间
	   ENDS CURRENT_TIMESTAMP/'YYYY-MM-DD HH:MM.SS' { + INTERVAL 1 [HOUR|MONTH|WEEK|DAY|MINUTE|...] } */

-- [ON COMPLETION [NOT] PRESERVE] 
-- 可选项，默认是ON COMPLETION NOT PRESERVE 即计划任务执行完毕后自动drop该事件；ON COMPLETION PRESERVE则不会drop掉。

-- [ENABLE | DISABLE] 
-- 设定event的状态，默认ENABLE：表示系统尝试执行这个事件。DISABLE：关闭该事情，可以用alter修改

-- [COMMENT 'comment'] 
-- 可选项，comment 用来描述event；相当注释，最大长度64个字节。

do
	begin
        /* 在这里写SQL语句或者调用存储过程 */
	    (sql_statements)
	end$$

delimiter ;
```



## 分区表

```
1、ADD PARTITION （新增分区）
2、DROP PARTITION （删除分区）
3、TRUNCATE PARTITION（截取分区）
4、COALESCE PARTITION（合并分区）
5、REORGANIZE PARTITION（拆分/重组分区）
6、ANALYZE 、CHECK PARTITION（分析与检查分区）
7、REPAIR分区
8、OPTIMIZE 优化分区
9、REBUILD分区
10、EXCHANGE PARTITION（分区交换）
11、迁移分区（DISCARD 、IMPORT ）
```



```sql
SHOW VARIABLES LIKE '%partition%';
```



## mysql的数据回滚(闪回)

闪回思路

参考链接

```html
https://www.jb51.net/article/99553.htm
https://github.com/danfengcao/binlog2sql
https://github.com/danfengcao/binlog2sql
https://blog.csdn.net/root__oo7/article/details/84028986
```



## mysql快速备份表

```sql
DROP TABLE IF EXISTS B;
CREATE TABLE B LIKE A;
INSERT INTO B SELECT * FROM A;
```



## mysql插入指定的字段的写法

```sql
insert into fhcd_tk(tyshxym,sqrq,btje,tkr,tkrq,tkbz) select tyshxym,sqrq,btje,'tkr',now(),'1' from fhcd
```



## MySQL中创建表时AUTO_INCREMENT的用法

在表app的数据中，字段app_id中的最大值为9，而创建表的时候AUTO_INCREMENT=12，则下次通过程序往表app中insert数据，且没有指定字段app_id的值时，下次的app_id将从12开始计算起，而不是10，切记，切记。







## mysql的开窗函数排序

#### mysql5.7

```sql
drop table ih.`tem11`;
CREATE TABLE ih.`tem11` (
	`id` INT ( 11 ) NOT NULL AUTO_INCREMENT,
	`str` CHAR ( 1 ) DEFAULT NULL,
PRIMARY KEY ( `id` ))
ENGINE=InnoDB COMMENT='test';

INSERT INTO  ih.`tem11` ( `id`, `str` ) VALUES ( 1, 'A' );
INSERT INTO  ih.`tem11` ( `id`, `str` ) VALUES ( 2, 'B' );
INSERT INTO  ih.`tem11` ( `id`, `str` ) VALUES ( 3, 'A' );
INSERT INTO  ih.`tem11` ( `id`, `str` ) VALUES ( 4, 'C' );
INSERT INTO  ih.`tem11` ( `id`, `str` ) VALUES ( 5, 'A' );
INSERT INTO  ih.`tem11` ( `id`, `str` ) VALUES ( 6, 'C' );
INSERT INTO  ih.`tem11` ( `id`, `str` ) VALUES ( 7, 'B' );
select * from ih.`tem11`;
SELECT
    @num := IF(@str = str, @num + 1, 1) num,
    id,
    @str := str str
FROM
    ih.`tem11`, (SELECT @str := '', @num := 0) t1
ORDER BY
    str, id;
		
```



#### Mysql8.0+

mysql从8.0开始支持开窗函数

[mysql官方帮助文档]: https://dev.mysql.com/doc/refman/8.0/en/window-function-descriptions.html#function_dense-rank

```sql
SELECT
	row_number() over ( PARTITION BY user_id ORDER BY gmt_create DESC ) AS row_num,
	user_id,
	order_id,
	receiver_province,
	receiver_city,
	receiver_address,
	provider_id 
FROM
	mall.`orders`
```



# Mysql的运维



### 查看mysql的磁盘容量

```sql
select concat(round(sum(DATA_LENGTH/1024/1024),2),'MB') as data from information_schema.TABLES;
select concat(round(sum(DATA_LENGTH/1024/1024),2),'MB') as data from information_schema.TABLES;
SELECT CONCAT(ROUND(SUM(DATA_LENGTH/1024/1024/1024), 2), 'G') FROM information_schema.tables;

```

#### 查询mysql的表库的数据量

```sql
--查看所有数据库容量大小
select
table_schema as '数据库',
sum(table_rows) as '记录数',
sum(truncate(data_length/1024/1024, 2)) as '数据容量(MB)',
sum(truncate(index_length/1024/1024, 2)) as '索引容量(MB)'
from information_schema.tables
group by table_schema
order by sum(data_length) desc, sum(index_length) desc;

--查看所有数据库各表容量大小
select
table_schema as '数据库',
table_name as '表名',
table_rows as '记录数',
truncate(data_length/1024/1024, 2) as '数据容量(MB)',
truncate(index_length/1024/1024, 2) as '索引容量(MB)'
from information_schema.tables
order by data_length desc, index_length desc;

```



### linux扩大端口的使用量和短连接的回收时间

```
cat /proc/sys/net/ipv4/ip_local_port_range
cat /proc/sys/net/ipv4/tcp_fin_timeout
```



### dba常用命令

```sql
-- mysql显示的排序方式
show COLLATION;
-- 展示前一条的告警信息
show WARNINGS;
-- 展示可用引擎的命令
show ENGINES;
SHOW VARIABLES LIKE 'have%';
-- 获取表的引擎
SELECT table_name,engine FROM information_schema.tables

-- SELECT @@tx_isolation,@@global.tx_isolation;

-- 查看是否是自动提交
SELECT @@autocommit;

-- 查询sql_mode
SELECT ROUTINE_SCHEMA, ROUTINE_NAME, SQL_MODE FROM INFORMATION_SCHEMA.ROUTINES;
SELECT EVENT_OBJECT_SCHEMA, EVENT_OBJECT_TABLE, TRIGGER_NAME, SQL_MODE FROM INFORMATION_SCHEMA.TRIGGERS;
```

### mysql的参数配置





# Navicat 使用

## 1、导出查询出来的结果

注意查询的名字要是表名











```
DATE_FORMAT() 函数用于以不同的格式显示日期/时间数据。
Sql代码  收藏代码
DATE_FORMAT(date,format)  
 
可以使用的格式有：
格式 描述
%a  缩写星期名
%b  缩写月名
%c  月，数值  SELECT DATE_FORMAT('2020-04-05','%c') FROM DUAL   - -  4
%D  带有英文前缀的月中的天
%d  月的天，数值(00-31)
%e  月的天，数值(0-31) SELECT DATE_FORMAT(SYSDATE(),'%e');
%f  微秒
%H  小时 (00-23)
%h  小时 (01-12)
%I  小时 (01-12)
%i  分钟，数值(00-59)
%j  年的天 (001-366)
%k  小时 (0-23)
%l  小时 (1-12)
%M  月名
%m  月，数值(00-12)
%p  AM 或 PM
%r  时间，12-小时（hh:mm:ss AM 或 PM）
%S  秒(00-59)
%s  秒(00-59)
%T  时间, 24-小时 (hh:mm:ss)
%U  周 (00-53) 星期日是一周的第一天
%u  周 (00-53) 星期一是一周的第一天
%V  周 (01-53) 星期日是一周的第一天，与 %X 使用
%v  周 (01-53) 星期一是一周的第一天，与 %x 使用
%W  星期名
%w  周的天 （0=星期日, 6=星期六）
%X  年，其中的星期日是周的第一天，4 位，与 %V 使用
%x  年，其中的星期一是周的第一天，4 位，与 %v 使用
%Y  年，4 位
%y  年，2 位
```

# mysql学习笔记

```
day01
	1、关系结构数据库：使用二维表格来存储
	2、常见数据库：
	     Oracle、DB2(IBM)、SQL Sever、Sybase、Mysql
	3、RDBMS=管理员(manger)+仓库(database)
	        databse=N个table
	        table:表结构：定义表的列名和列类型
	              表记录：一行行的记录
	4、java应用与数据库的关系
	   中间：网络数据传输
	5、安装mysql
				安装文件所在路径：不能有空格和中文
				选择开发者电脑
				mysql中设置编码时为UTF8而不是UTF-8
				Include BIn Diretory in windows PATH:把mysql设置到环境变量Path中
					---压缩包的版本
						--解压到指定的文件
						--新建一个data文件夹
	6、删除mysql
	   停止Mysql(cmd中停止net stop mysql;手动停止)
	   添加删除程序卸载mysql
	   到安装目录删除mysql
	   删除：C:\Documents and Settings\All Users\Application Data\Mysql
	           C:\ProgramData\Mysql
	    regedit
	    查看注册表：
	    HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services
	    HKEY_LOCAL_MACHINE\SYSTEM\ControlSet001\Services
	    HKEY_LOCAL_MACHINE\SYSTEM\ControlSet002\Services
	7、mysql的配置信息
	     任务管理器中mysqld.exe(服务器端程序)表示启动
	       my.ini表示服务器的配置文件port=3306
	     配置字符编码
	     【client】下配置客户端编码：default-character-set=gbk
	     【mysqld】下配置服务器编码：default-set-sever=utf8
	     配置二进制数据大小上限：在【mysqld】下配置：max_allowed_packet=8M
			   数据库的文件在C:\ProgramData(默认为隐藏文件)
			   登陆服务器
			   >-u ：后面更随便用户名
			   >-p:  后面跟随密码
			   >-h:  后面跟随IP(root下可以省略IP)
			   退出服务器：exit或quit
	8、SQL语言概述
	   结构化语言(ISC国际化标准)、方言
	   sql语法
	     SQL语句可以在单行或多行书写，以分号结尾
	     可使用空格和缩进来增强语句的可读性
	     Mysql不区别大小写，但建议大写
	    sql语句分类
	    DDL(数据定义语言：数据库或表的结构操作)
	    |DML(数据操作语言：对表的记录进行更新(增、删、改))
	    |DQL：对表的记录的查询(难点)
	    |DCL：对用户的创建，及授权！
	9、DDL之操作数据库
	    查看所有数据库：SHOW DATABASES;
	    切换(选择要操作的)数据库：USE 数据库名;
	    创建数据库：CREATE DATABASE [IF NOT EXISTS] mydb1 [CHARSET=utf]//[]不需要写
	    删除数据库：DPOR DATABASE [IF EXISTS] mydb2;
	    修改数据库编码：ALTER DATABASE mydb1 CHARACTER SET utf8
	10、mysql数据类型
	    int、double{字符串类型double(5,2)表示最多5位，其中必须有2位小数，及最大值为999.99}
	    decimal{浮点型，在表单钱方面使用类型，因为不会出现精度缺失的问题(在java中的BigDecimal)}
	    char:(固定长度字符串类型char(255)(数据长度不足时，补足到指定长度))
	    varchar:(可变长度字符串类型varchar(65535)(浪费一个字节存储长度))
	    char比varchar节省空间，但后者插入时不会补0
	    text(标准的是clob)【独有类型】:字符串类型：
	    blob：和text相同，只是是二进制的
	    longblob：表示一行4G的内容
	    data：日期类型，格式为：yyyy-MM-dd;
	    time: 时间类型，格式为：hh:mm:ss;
	    timestamp:时间戳类型：包含yyyy-MM-dd和hh:mm:ss
	11、DDL之操作表
	  创建表：
	    CREATE TABLE[IF NOT EXISTS] 表名(
	     列名 列类型,
	     列名 列类型,
	     ...
	     列名 列类型
	     );
	   查看当前数据库中所有表名称：SHOW TABLES;
	   查看指定表的创建语句：SHOW CREATE TABLE 表名(了解);
	   查看表结构:DESC 表名;
	   删除表：DROP TABLE 表名;
	   修改表：
	      >前缀：ALTER TABLE 表名;
	      >添加列：
	      ALTER TABLE 表名 ADD(
	      列名 列类型，
	      列名 列类型，
	      ...);
	      >修改列类型(如果被修改的列存在数据，那么修改的类型可能会影响到已存在数据):
	      ALTER TABLE 表名 MODIFY  列名 列类型;
	      >修改列名:
	      >删除列：ALTER TABLE 表名 DROP 列名;
	      >修改表名称:ALTER TABLE 原表名 RENAME TO 新表名;
	12、DML之INSERT
	    DQL  select *from emp(表); 查询表记录
	    DML(数据操作语言，他是对表记录操作(增删改)！)
	    1、插入数据
	          INSERT TO 表名(列名1，列名2，...) VALUES(列值1，列值2,...);
	          在表名后要插入的列名，其他没有指定的列等同与插入null值。所以插入记录总是插入一行。不可能半行。(在数据库中所有的字符串类型，必须使用单引，不能使用双引号。
	          日期类型也要使用单引号)
	             在values后给出列值，值的顺序和个数必须与前面指定的列对应
	      *INSERT INTO 表名 VALUES(列值1,列值1)
	        >没有给出要插入的列值，那么表示插入所有列(但是可读性降低)
	        >值的个数必须是该表列的个数
	        >值的顺序，必须与表创建时给出的列的顺序相同
	         //插入部分列，没有插入的列，默认的为null
	         INSERT INTO STU(
	         number,name
	         )VALUES(
	         'iconic'，'lisi'
	         )
	    2、修改数据
	          UPDATE 表名 SET 列名1=列值1,列名2=列值2,[where 条件]
	    3、删除数据
	          DELETE FROM 表名【WHERE 条件】//不加where直接删除表，备份
	          TRUNCATE TABLE 表名：TRUNCATE是DDL语句，它是先删除drop该表，再create该表。而无法回滚。
	          条件:
	          运算符：=、！=、<>、>、<、>=、<=、BETWEEN...AND、IN(...)、IS NULL、NOT、OR、AND
	          注意：
	          update th_student set age=10 where age is null;(其中不可以换成age=null)
	13、DCL
	    一个项目创建一个用户！一个项目对应的数据库只有一个！
	    这个用户只能对这个数据库有权限，其他数据库就操作不了。
	   1、创建用户
	     CREATE USER 用户名@IP地址 IDENTIFIED BY '密码';
	      >用户只能在指定的IP地址上登录
	     CREATE USER 用户名@'%' IDENTIFIED BY '密码';
	      >用户可以在任意IP地址上登录
	    2、给用户授权
	      GRANT 权限1，...,权限n ON 数据库.* TO 用户名@IP地址
	        >权限、用户、数据库
	        >给用户分派在指定的数据库上的指定的权限
	        >例如：GRANT CREATE,ALTER,DROP,INSER on mysdb3.* to zhangsan@locahost
	      GRANT ALL ON 数据库.* TO 用户名@IP地址
	      >给用户分派指定数据库上的指定权限
	    3、撤销授权
	      REVOKE 权限1、...权限n ON 数据库.* FROM 用户名@IP地址;
	       >撤销指定用户在指定数据库上的指定权限
	    4、查看权限
	    SHOW GRANTS FOR 用户名@IP地址
	     >查看只当用户的权限
	    5、删除用户
	    DROP USER 用户名@IP地址
	14、DQL-数据基本查询
	    一、基本查询
	    1、字段(列)控制
	     SELECT *FROM 表名;
	     SELECT *FROM emp;
	     -->其中‘*’表示查询所有列
	    2、查询指定列
	     SELECT 列1 [,列2,...列N] FROM 表名;
	     SELECT empno,ename,sal,conn FROM 表名;
	    3、完全重复的记录一次
	       去除完全重复行、
	       SELECT DISTINCT * 列 FROM 表
	    4、列运算
	       I 数量类型的列可以做加、减、乘、除运算
	        SELECT SAL*1.5 FROM EMP;
	        SELECT SAL+COMM FROM EMP;
	       II 字符串类型可以做连续运算
	        SELECT CONCAT('$',sal) FROM emp;
	       III 转换NULL值
	       有时需要把NULL值转换为其它的值，列如com+1000时，如果com列存在NULL值，那么NULL+1000还是NULL,而我们这时希望把NULL当成0来运算。
	       SELECT IFNULL(COMM,0) FROM EMP;:如果comm存在NULL值，那么当成0来运算。
	       IV 给列起别名
	       你也许已经注意到了，当使用列运算后名称很不好看，这时需要给列名起别名，这样在结果集中列名就显示别名了
	         SELECT IFNULL (COMM,0)+1000 AS 奖金 FROM emp;
	         -->其中AS可以省略
	         SELECT EMPNO,ENMAE JOB,SAL,COMM FROM EMP;
	         //其中ENMAE JOB表示起别名
	    二、条件控制
	    1)条件查询
	      与前面的UPDATE和DELETE 语句一样，SELECT语句也可以使用WHERE子句来控制记录
	       *SELECT empno,ename,sal,comm FROM emp WHERE sal>10000 AND comm IS NOT NULL;
	       *SELECT empno,ename,sal FROM emp WHERE sal BETWEEN 2000 AND 3000;
	       *SELECT empno,ename,job FROM emp WHERE job IN('经理','董事长');
	    2)模糊查询
	       当你查询姓张，并且姓名一共两个字的员工时，这时就可以使用模糊查询；
	          SELECT * FROM emp WEHERE ename Like '张_';//匹配一个字符
	        -->模糊查询需要使用运算符：LIKE,其中匹配一个任意字符，注意了只匹配一个字符而不是多个。
	        -->上面的语句查询的是姓张，名字由两个字组成的员工。
	         SELECT *FROM emp WHERE ename LIKE '___';/*姓名由3个字组成的员工*/

	         如果想查询姓张的，名字几个字可以的员工就要使用'%'了；
	        SELECT * FROM emp WHERE ENMAE LIKE '%张';
	        -->其中%匹配0-N个任意字符，所以上面语句查询的是姓张的所有员工
	         SELECT * FROM emp WHERE ENMAE LIKE '%啊%';
	        -->千万不要以为查询的是姓名中带有‘啊’字的员工，姓名以啊开头和结尾的员工也都会查询到。
	        SELECT * FROM emp WHERE ENMAE LIKE '%';
	        -->这个条件等同于不存在，但如果姓名为NULL的查询不出来！
	    三、排序
	    1)升序
	      SELECT *FROM WHERE emp ORDER BY sal ASC;
	      -->按sal排序，升序！
	      -->其中ASC是可以省略的
	    2)降序
	      SELECT *FROM WHERE emp ORDER BY comm DESC;
	      -->按comm排序，降序；
	      -->其中DESC不能省略
	    3)使用多列作为排序条件
	      SELECT *FROM WHERE emp ORDER BY sal ASC,comm DESC;
	      -->使用sal升序排，如果sal相同时，则使用comm的降序牌
	    四、聚合函数
	    聚合函数用来做某列的纵向运算。
	       1)COUNT
	         SELECT COUNT(*) FROM emp;
	         -->计算emp表中所有列都不为NULL的记录的行数
	         SELECT COUNT(comm) FROM emp;
	         -->云计算emp表中comm列部位NULL的记录的行数
	       2)MAX
	        SELECT MAX(sal) FROM emp;
	        -->查询最高工资 
	       3)MIN
	         SELECT MIN(sal) FROM emp;
	         -->查询最低工资
	       4)SUM
	         SELECT SUM(sal) FROM emp;
	         -->查询工资集合 
	       5)AVG
	         SELECT AVG(sal) FROM emp;
	         -->查询平均工资
	         select count(*) 人数，sum(sal) 总和，max(sal) 最高 from  emp; 
	    五、分组查询
	         分组查询是把记录使用某一列进行分组，然后查询组信息。
	           主信息+聚合函数
	         例如：查看所有部门的记录数。
	         SELECT deptno,COUNT(*) FROM emp GROUP BY deptno;
	         -->使用deptno分组，查询部门编号和每个部门的记录数
	         SELECT job ,MAX(SAL) FROM emp GROUP BY job;
	         -->使用job分组，查询每种共工作的最高工资
	         组条件
	         分组前条件
	         SELECT deptno,count<*> from emp where sal>15000 group by deptno;
	         分组后条件：
	         以部门分组，查询每组记录数，条件为就路数大于3
	         SELECT deptno,COUNT(*) FROM emp GROUP BY deptno HAVING COUNT(*)>3;
	         ===================
	         select
	         from
	         group by
	         having
	         order by
	         ===================
	    六、limit子句(方言)
	        LIMIT用来限定查询结果的起始行，以及总行数。
	        例如：查询起始行为第5行，一共查询3行记录。
	        SELECT *FROM emp LIMIT 4,3;
	        -->其中4表示从第5行开始，其中3表示一共查询3行。
	        select *from emp limit 0,5;
	               分页
	            1、一页的记录数：10行
	            2、查询第3行
	            select * from emp limit 20,10;
	            (当前页-1)*每页记录数
	            (3-1)*10
	            (17-1)*8,8
day02
	一、mysql的编码问题
	    1、查询编码mysql数据库编码
	      *SHOW VARIABLES LIKE 'char%';
	    2、编码解释
	      *character_set_client:mysql使用该编码来解读客户端发送过来的数据，例如该编码为UTF8，那么如果客户端发送过来的数据不是UTF8,那么就会出现乱码
	      *character_set_results:mysql会把数据转换成该编码后，再发送给客户端。例如该编码为UTF8,那么如果客户端不使用UTF8来解读，那么就会出现乱码，其他编码只支持中文即可，也就是说不能使用latinl
	    3、控制台乱码问题
	      *插入或修改时出现乱码；
	      >这时因为cmd下默认使用GBK，而character_set_client不是GBK的原因，我们只需让这两个编码相同即可。
	      >因为修改cmd的编码不方便，所以我们去设置character_set_client为GBK即可
	     *查询出的数据为乱码：
	      >这是因为character_set_results不是GBK，而cmd默认使用GBK的原因。我们只需要让这两个编码相同即可。
	      >因为修改cmd的编码不方便，所以我们去设置character_set_results为GBK即可。
	      *设置变量的语句：
	       >set character_set_client=gbk;
	       >set character_set_results=gbk;

	     注意：设置变量只对当前连续有效，当退出窗口后，再次登陆mysql,还需要再次指定编码。为了一劳永逸，可以在my.ini中设置：default-character-set=gbk即可。
	     client、results、connection方式
	     4、指定默认编码
	      我们在安装mysql时已经指定了默认为UTF8，所以我们在创建数据库、创建表时，都无需再次指定编码。
	      为了一劳永逸，可以在my.ini中设置： character-set-sever=utf8即可;
	     -------------------------------------------------------------------------------
	      Variable_name Value
	      character_set_client  utf8  //无论客户端发送的什么样的编码格式，都当成是utf8格式(若客户端发送的是GBK，则必乱码)
	      character_set_connection  utf8
	      character_set_database  utf8
	      character_set_filesystem  binary
	      character_set_results utf8 //修改
	      character_set_server  utf8
	      character_set_system  utf8
	      character_sets_dir  C:\\Program Files\\MySQL\\MySQL Server 5.6\\share\\charsets\\
	      --------------------------------------------------------
	二、备份与恢复 
	  1、数据库导出SQL脚本(备份数据库内容，并不是备份数据库)
	    >mysqldump -u用户名 -p密码 数据库名>生成的脚本文件路径
	    >例如：mysqldump -uroot -p123 mydb1>c:\mydb1.sql(与mysql.exe和mysqld.exe一样，都在bin目录下)
	    >注意：不要打分号，不要登录mydb1,直接在cmd下运行
	    >注意：生成的脚本文件中不包含create datase语句
	  2、执行sql脚本
	     第一种方式
	     >mysql -u用户名 -p密码 数据库<脚本文件路径
	     >例如：
	       *先删除mydb1库，再重新创建mydb1库
	       *mysql -uroot -p123 mydb1<c:\mydb1.sql
	     >注意，不要打分号，不要登录mysql，直接在cmd下运行。

	     第二种方式
	     >登录mysql
	     >source SQL脚本路径
	     >例如：
	        *先删除mydb1库，再重新创建mydb1库
	        *切换到mydb1库
	        *source c:\mydb1.sql
	     数据库-->sql:备份
	     sql-->数据库：恢复
	三、约束
	  *约束是添加在列上的，用来约束列的!
	  1、主键约束(唯一标识)
	  ****非空*****
	  ****唯一*****
	  ****被引用***(外键)
	  *当表的某一列被指定为主键后，该列就不能为空，不能有重复值出现。
	  *当创建表时指定主键的两种方式：
	    >CREATE TABLE stu(
	        sid    CHAR(6) PRIMARY KEY,
	        snme   VARCHAR(20),
	        age         INT,
	        gender   VARCHAR(10)
	        );
	        只当sid列为主键列，即为sid列添加主键约束
	      >CREATE TABLE stu(
	        sid    CHAR(6) ,
	        snme   VARCHAR(20),
	        age         INT,
	        gender   VARCHAR(10),
	        PRIMARY KEY(sid)
	        );
	        指定sid列为主键列，即为sid列添加主键约束
	        *修改表时指定主键：ALTER TABLE stu ADD PRIMARY KEY(sid);
	        *删除主键：ALTER TABLE stu DROP PRIMARY KEY;
	        ADD-->添加列
	        MODIFY-->修改列名和列类型
	        CHANGE-->修改列名
	        DROP-->删除列
	        RENAME TO-->修改表名
	  2、主键自增长(群集环境下不好用)
	    *因为主键列的特性是：必须唯一、不能为空，所以我们通常会指定主键类为整型，然后设置其自动增长，这样可以保证在插入数据时主键列的唯一和非空特性
	    *创建表时指定主键自增长
	    >CREATE TABLE stu(
	        sid    CHAR(6) PRIMARY KEY AUTO_INCREMENT,
	        snme   VARCHAR(20),
	        age         INT,
	        gender   VARCHAR(10)
	        );
	      *修改表时设置主键自增长：ALTER TABLE stu CHANGE sid sid INT AUTO_INCREMENT;
	      *修改表时删除主键自增长：ALTER TABLE stu CHANGE sid sid INT;
	      *测试主键自增长：
	       >INSERT INTO stu VALUES(NULL,'zhangsan',23,'male');
	       >INSERT INTO stu(sanme,age,gender)VALUES('zhangsan',23,'male');

	       群集现象(多台数据库共查询)
	       UUID(不重复)
	  3、非空约束
	    *因为某些列不能设置为null值，所以可以对列添加非空约束。
	    例如：
	        >CREATE TABLE stu(
	        sid    INT PRIMARY KEY AUTO_INCREMENT,
	        snme   VARCHAR(20) NOT NULL,
	        age         INT,
	        gender   VARCHAR(10)
	        );
	      *对sname列设置了非空约束
	  4、唯一约束
	    *因为某些列不能设置重复的值，所以可以对列添加唯一约束
	   例如：
	           >CREATE TABLE stu(
	        sid    INT PRIMARY KEY AUTO_INCREMENT,
	        snme   VARCHAR(20) NOT NULL UNIQUE,
	        age         INT,
	        gender   VARCHAR(10)
	        );
	    *对sname列设置了非空约束
	  5、概念模型
	      对象模型：可以双向关联，而且引用的是对象，而不是一个主键！
	      关系模型：只能多方引用一方，而且引用的只是主键，而不是一整行记录！
	     对象模型：在java中是domain!!例如：User\Student
	      is a //表示继承
	       has a// 关联
	         >1对1
	         >1对多
	         >多对多
	       use a //方法
	     数据模型：在数据库中就是表
	     当我们要完成一个软件系统时，需要把系统中的实体抽取出来，形成概念模型。
	     例如部门、员工都是系统中的实体，概念模型中的实体最终会成为java中的类、数据库中表。
	     实体之间还存在着关系，关系有三种：
	        *1对多|1对1|多对多
	       概念模型在java中成为实体类(javaBean)
	       类就使用成员变量来完成关系，一般都是双向关联；
	       多对一双向中关联，即员工关联部门，部门也关联员工。
	     class Employee{//多方关联一方
	       ...
	        private Deparment deparment;}
	     class deparment{//一方关联多方
	       ...
	        private List<Employee> employees;}
	     class Husband{
	     ...
	     private Wife wife;
	     }
	     class Wife{
	     ...
	     private Husband husband;
	     }

	     class Student{
	     ...
	     private List<Teacher> teachers;
	     }
	     class Teacher{
	     ...
	     private List<Student> students;
	     }
	  6、外键约束
	    *外键必须是另一表的主键的值(外键要引用主键)
	    *外键可以重复
	    *外键可以为空
	    *一张表可以有多个外键
	    数据模型在数据库中为表
	    数据库表中的多对一关系，只需要在多方使用一个独立的列来引用1方的主键即可
	    CONSTRAINT fk_emp_dept FOREIGN KEY(deptno//另一个表的主键)REFERENCES dept(deptno);
	    语法：CONSTRAINT 约束名称 FOREIGN KEY(外键列名) REFERENCES 关联表(关联表的主键)
	    create table emp(
	     empno int primary key,
	     ..
	     deptno int,
	     CONSTRAINT fk_emp_dept FOREIGN KEY(mgr) REFERENCES dept(deptno)
	     );
	    修改表时添加外键约束
	     ALERT TABLE emp;
	     ADD CONSTRAINT fk_emp_dept FOREIGN KEY(mgr) REFERENCES dept(deptno)
	    修改表时删除外键约束
	     ALERT TABLE emp;
	     DROP CONSTRAINT fk_emp_dept FOREIGN KEY(mgr) REFERENCES dept(deptno)
	  7、数据库一对一关系
	   在表中建立一对一关系比较特殊，需要让其中一张表的主键，即是主键又是外键
	   create table husband(
	     hid int PRIMARY KEY,
	     ..);
	   create table wife(
	     wid int PRIMARY KEY,
	     ...
	     ADD CONSTRART fk_wife FOREIGN KEY(wid) REFERENCES husband(hid)
	    );
	    其中wife表的wid及是主键，又是相对husband表的外键！
	    husband.hid是主键，不能重复 ！
	    wife.wid是主键，不能重复 ，又是外键，必须来自husband.hid。
	    所以如果在wife表中有一条记录的wid为1,那么wife表中的其他记录dewid就不能再是1了，因为它是主键。
	    同时在husband.hid中必须在1这个值，因为wid是外键。这就完成一对一关系。

	    例：
	    CREATE TABLE hashand(
	    hid INT PRIMARY KEY AUTO_INCREMENT,
	    hname VARCHAR(50));
	    ALTER TABLE hashand
	    RENAME TO hasband
	      INSTERT TO hasband VALUES(NULL,'刘备');
	      INSTERT TO hasband VALUES(NULL,'关羽');
	      INSTERT TO hasband VALUES(NULL,'张飞');
	    总结：从表的主键既是外键
	  8、数据库多对多关系
	    在表中建立多对多关系需要使用中间表，即需要三张表，在中间表使用的是两个外键，分别引用其他两个表的主键。
	    create table student(
	    sid int PRIMARY KEY,
	    ...);
	    create table teacher(
	    tid int PRIMARY KEY,
	    ...);    
	    create table stu_tea(
	    sid int,
	    tid int,
	    ADD CONSTRAINT fk_stu_tea FOREIGN KEY(sid) REFERENCES student(sid
	    ADD CONSTRAINT fk_stu_tea FOREIGN KEY(tid) REFERENCES student(tid);    ...);

	    插入值的时候：
	    INSERT INTO stu_tea VALUES(1,1);
	  9、多表查询
	    1、分类
	      *合并结果集
	      *连接查询
	      *子查询
	  10、合并结果集(只要两个表的结果集相同就好)
	     *要求被合并的表中，列的类型和列数相同
	     *UNION:去除重复行
	     *UNION ALL:不去除重复行
	  11、连接查询
	    1、分类
	     *内连接
	     *外连接
	      >左外连接
	      >右外连接
	      >全外连接(mysql不支持)
	     *自然连接(属于一种简化方式)
	    2、内连接
	      *方言：SELECT *FROM 表1 别名1 ，表2 别名2 WHERE 别名1.xx=别名2.xx
	      *标准：SELECT *FROM 表1 别名1 INNER JOIN 表2 别名2 ON 别名1.xx=别名2.xx
	      *自然: SELECT *FROM 表1 别名1 NATYRAL JOIN 表2 别名2
	      *内连接查询出的所有纪录都满足条件。
	    3、外连接
	      *左外：SELECT *FROM 表1 别名1 LEFT OUTER JOIN 表2 别名2 ON 别名1.xx=别名2.xx
	       >左表记录无论是否满足条件都会查询出来，而右表只有满足条件才能出来。左表中不满足条件的记录，右表部分都为null
	      *左外自然：SELECT *FROM 表1 别名1 NATURAL LEFT OUTER JOIN 表2 别名2 ON 别名1.xx=别名2.xx
	      *右外：SELECT *FROM 表1 别名1 NATURAL RIGHT OUTER JOIN 表2 别名2 ON 别名1.xx=别名2.xx
	       >右表记录无论是否满足条件都会查询出来，而左表只有满足条件才能出来。右表中不满足条件的记录，左表部分都为null
	      *右外自然：SELECT *FROM 表1 别名1 NATURAL RIGHT OUTER JOIN 表2 别名2 ON 别名1.xx=别名2.xx
	      *全连接：(将上面两片代码)可以使用UNION来完成全链接
	      
	      

	  12、子查询
	    ：查询中有查询(查看select关键字的个数)
	    1、出现的位置
	     *where后作为条件存在
	     *from后作为表存在(多行多列)；
	    2、条件
	     *单行单列：SELECT * FROM 表1 别名1 WHERE 列1 [=、>、<、>=、<=、!=](SELECT 列 FROM 别名2 WHERE 条件)；
	     *多行单列：SELECT * FROM 表1 别名1 WHERE 列1 [IN,ALL,ANY](SELECT 列 FROM 别名2 WHERE 条件)；
	     *单行多列：SELECT * FROM 表1 别名1 WHERE 列1 [列1，列2] IN (SELECT 列 FROM 别名2 WHERE 条件)；

	     SELECT *FROM emp WHERE(job,deptno，sal) IN (SELECT job,deptno FROM emp WHERE ename='殷天正')
	     *多行多列：SELECT * FROM 表1 别名1 WHERE 列1 ,(SELECT ...)别名2 WHERE 条件
	     例题
	      /*查询最高工资*/
	      SELECT * FROM emp WHERE sal=MAX(SELECT MAX(sal) FROM emp);
	  ----------
	  笛卡尔积
	  {a,b,c}、{1，2}
	  {a1,a2,b1,b2,c1,c2}







Innodb的版本

​```mysql
select @@version; #数据库版本
select @@innodb_version;#innodb的版本
show engine innodb status\G;
​```

sysbench压力测试



















```

