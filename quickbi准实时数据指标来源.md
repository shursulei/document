## quickbi准实时数据指标来源

#### 服务器

ssh -t -p7711 sysop@39.106.91.147 ssh -t -p7711 sysop@10.1.6.211

#### 位置

crontab

*/5 * * * *  sleep 60;  nohup sh /opt/hlj_realtime_kettle/hlj_realtime_kettle_start.sh &

服务

sysop     70881      1  0 11月22 ?      00:03:02 python3 manage.py

#### 代码位置

https://git.helijia.cn/bi/dw_kettle.git



/opt/hlj_realtime_kettle/db_connect_pro/db_connect_set.ktr





# kettle

国内开源kettle镜像下载地址：http://mirror.bit.edu.cn/pentaho/Data%20Integration/7.1/

介绍：kettle是一个跨平台的etl工具,是一个java平台

安装：

1、默认需要安装jdk,需要去oracle官方文档下载安装。

2、下载解压安装,使用terminal执行 sh spoon.sh脚本打开会话的见面。



# quickbi准实时梳理

## quickbi准实时流程图

![image-20201214182345554](/Users/hanfeng/Documents/workspace/helijiaworkspace/quickbi准实时数据指标来源.assets/image-20201214182345554.png)

### 流程节点重点梳理

#### 设置并初始化数据库环境

初始化配置数据库相关信息，包括dev/pro版本

#### 设置时间字段

时间初始化now_datetime,设置全局变量

```sql
-- select NOW();
-- SELECT SUBSTR(NOW(),1,14);
-- select DATE_FORMAT(NOW(),'%i');
SELECT
	CASE 
	WHEN 
		DATE_FORMAT(NOW(),'%i') BETWEEN 0 AND 4 
	THEN 
		DATE_FORMAT(CONCAT(SUBSTR(NOW(),1,14),'00:00'),'%Y-%m-%d %H:%i:%s')
	WHEN 
		DATE_FORMAT(NOW(),'%i') BETWEEN 5 AND 9 
	THEN 
		DATE_FORMAT(CONCAT(SUBSTR(NOW(),1,14),'05:00'),'%Y-%m-%d %H:%i:%s') 
	WHEN 
		DATE_FORMAT(NOW(),'%i') BETWEEN 10 AND 14 
	THEN 
		DATE_FORMAT(CONCAT(SUBSTR(NOW(),1,14),'10:00'),'%Y-%m-%d %H:%i:%s')
	WHEN 
		DATE_FORMAT(NOW(),'%i') BETWEEN 15 AND 19 
	THEN 
		DATE_FORMAT(CONCAT(SUBSTR(NOW(),1,14),'15:00'),'%Y-%m-%d %H:%i:%s')
	WHEN 
		DATE_FORMAT(NOW(),'%i') BETWEEN 20 AND 24 
	THEN 
		DATE_FORMAT(CONCAT(SUBSTR(NOW(),1,14),'20:00'),'%Y-%m-%d %H:%i:%s')
	WHEN 
		DATE_FORMAT(NOW(),'%i') BETWEEN 25 AND 29 
	THEN 
		DATE_FORMAT(CONCAT(SUBSTR(NOW(),1,14),'25:00'),'%Y-%m-%d %H:%i:%s')
	WHEN 
		DATE_FORMAT(NOW(),'%i') BETWEEN 30 AND 34 
	THEN 
		DATE_FORMAT(CONCAT(SUBSTR(NOW(),1,14),'30:00'),'%Y-%m-%d %H:%i:%s')
	WHEN 
		DATE_FORMAT(NOW(),'%i') BETWEEN 35 AND 39 
	THEN 
		DATE_FORMAT(CONCAT(SUBSTR(NOW(),1,14),'35:00'),'%Y-%m-%d %H:%i:%s')
	WHEN 
		DATE_FORMAT(NOW(),'%i') BETWEEN 40 AND 44 
	THEN 
		DATE_FORMAT(CONCAT(SUBSTR(NOW(),1,14),'40:00'),'%Y-%m-%d %H:%i:%s')
	WHEN 
		DATE_FORMAT(NOW(),'%i') BETWEEN 45 AND 49 
	THEN 
		DATE_FORMAT(CONCAT(SUBSTR(NOW(),1,14),'45:00'),'%Y-%m-%d %H:%i:%s')
	WHEN 
		DATE_FORMAT(NOW(),'%i') BETWEEN 50 AND 54 
	THEN 
		DATE_FORMAT(CONCAT(SUBSTR(NOW(),1,14),'50:00'),'%Y-%m-%d %H:%i:%s')
	WHEN 
		DATE_FORMAT(NOW(),'%i') BETWEEN 55 AND 59 
	THEN 
		DATE_FORMAT(CONCAT(SUBSTR(NOW(),1,14),'55:00'),'%Y-%m-%d %H:%i:%s')
	END AS now_datetime
FROM DUAL ;
```

#### 初始化数据库，删除该时间断的的数据

```sql
delete from order_sale_detail where start_tm='${NOW_DATETIME}'
```

#### 实时看板

##### 流程图

<img src="/Users/hanfeng/Documents/workspace/helijiaworkspace/quickbi准实时数据指标来源.assets/image-20201215105157029.png" alt="image-20201215105157029" style="zoom:50%;" />

##### 平品售卡

```sql
SELECT 
	   date_add('${NOW_DATETIME}',interval -5 minute) as r_date,
       u.city AS city_id,
       4 as sale_type_cd,
       1 as income_type_cd,
       SUM(u.face_value) money
  FROM `card_order` u
 WHERE u.pay_time>= date_add('${NOW_DATETIME}',interval -5 minute)
   AND u.pay_time< '${NOW_DATETIME}'
 GROUP BY u.city
```

插入更新表order_sale_detail

<img src="/Users/hanfeng/Documents/workspace/helijiaworkspace/quickbi准实时数据指标来源.assets/image-20201214193711888.png" alt="image-20201214193711888" style="zoom:25%;" />

##### 订单数

<img src="/Users/hanfeng/Documents/workspace/helijiaworkspace/quickbi准实时数据指标来源.assets/image-20201215110318099.png" alt="image-20201215110318099" style="zoom:50%;" />

订单详情并排序

```sql
select   date_add('${NOW_DATETIME}',interval -5 minute) as r_date ,  
         item_seq,
         order_seq,
         category  prod_cate_cd 
from order_item a
where pay_status= 1
and  pay_time  >= date_add('${NOW_DATETIME}',interval -5 minute) 
and  pay_time < '${NOW_DATETIME}'
group by item_seq,category,order_seq 

-- 增加排序
order  by order_seq,item_seq,prod_cate_cd
```

订单数并排序

```sql
select   date_add('${NOW_DATETIME}',interval -5 minute) as r_date ,  
         order_seq,
        shop_id  
from  `order` b 
where b.pay_time  >= date_add('${NOW_DATETIME}',interval -20 minute) 
and  b.pay_time < '${NOW_DATETIME}'
group by order_seq,shop_id ;
-- 增加排序
order by order_seq shop_id
```

订单详情和订单数的内连接：关联字段order_seq/r_date

字段选择：

![image-20201215105701618](/Users/hanfeng/Documents/workspace/helijiaworkspace/quickbi准实时数据指标来源.assets/image-20201215105701618.png)

分组统计

分组字段
r_date
prod_cate_cd
shop_id

聚合order_cnt

排序shop_id/prod_cate_cd 升序

手艺人信息

```sql
select 
	  artisan_id,
       type artisan_cate_cd,
       city city_id
from(
	select artisan_id, type, city
  	from artisan
 	where STATUS in(1, 12)
 	union all
	select studio_code, category, city_id
  	from ma2_studio
 	where status= 1
) a
 group by artisan_id,type,city
```

排序，字段是artisan_id

订单数和手艺人关联

shop_id=artisan_id

订单数字段

![image-20201215110802557](/Users/hanfeng/Documents/workspace/helijiaworkspace/quickbi准实时数据指标来源.assets/image-20201215110802557.png)

增加订单金额常量:order_amt

分组求和

<img src="/Users/hanfeng/Documents/workspace/helijiaworkspace/quickbi准实时数据指标来源.assets/image-20201215111009959.png" alt="image-20201215111009959" style="zoom:50%;" />

增加常量：

<img src="/Users/hanfeng/Documents/workspace/helijiaworkspace/quickbi准实时数据指标来源.assets/image-20201215111656666.png" alt="image-20201215111656666" style="zoom:50%;" />

插入或者更新到表中

order_sale_detail

<img src="/Users/hanfeng/Documents/workspace/helijiaworkspace/quickbi准实时数据指标来源.assets/image-20201215111854900.png" alt="image-20201215111854900" style="zoom:50%;" />

##### 新老订单

新老订单数据统计

![image-20201215112417227](/Users/hanfeng/Documents/workspace/helijiaworkspace/quickbi准实时数据指标来源.assets/image-20201215112417227.png)

订单

```sql
select concat(a.order_seq,'') as order_seq,
         a.category  prod_cate_cd ,
        b.shop_id  
from order_item a
LEFT JOIN `order` b 
on a.order_seq= b.`order_seq`
where a.pay_status= 1
 and  a.pay_time  >= date_add('${NOW_DATETIME}',interval -20 minute) 
 and  a.pay_time < '${NOW_DATETIME}'
 and  b.pay_time  >= date_add('${NOW_DATETIME}',interval -20 minute) 
 and  b.pay_time < '${NOW_DATETIME}'
group by a.order_seq,a.category,b.shop_id
```

排序

![image-20201215114023670](/Users/hanfeng/Documents/workspace/helijiaworkspace/quickbi准实时数据指标来源.assets/image-20201215114023670.png)

订单支付

```sql l
-- 订单支付
SELECT 
  date_add('${NOW_DATETIME}',interval -5 minute) AS r_date,
  concat(`order_seq`,'') as order_no,
  SUM(`paid_fee`/100) AS old_order_amt
FROM `order_payment`
WHERE `update_time` >= date_add('${NOW_DATETIME}',interval -5 minute)
AND `update_time` < '${NOW_DATETIME}'
AND `payment_type` = 3  
GROUP BY `order_seq`
```

排序

![image-20201215114254981](/Users/hanfeng/Documents/workspace/helijiaworkspace/quickbi准实时数据指标来源.assets/image-20201215114254981.png)

新订单和老订单的内连接关联

<img src="/Users/hanfeng/Documents/workspace/helijiaworkspace/quickbi准实时数据指标来源.assets/image-20201215114353688.png" alt="image-20201215114353688" style="zoom:50%;" />

老订单字段选择

![image-20201215133345747](/Users/hanfeng/Documents/workspace/helijiaworkspace/quickbi准实时数据指标来源.assets/image-20201215133345747.png)

根据shop_id排序

![image-20201215133426674](/Users/hanfeng/Documents/workspace/helijiaworkspace/quickbi准实时数据指标来源.assets/image-20201215133426674.png)

关联手艺人信息

分组求和，求出老订单的总合

![image-20201215133632043](/Users/hanfeng/Documents/workspace/helijiaworkspace/quickbi准实时数据指标来源.assets/image-20201215133632043.png)

更新订单表

![image-20201215133758797](/Users/hanfeng/Documents/workspace/helijiaworkspace/quickbi准实时数据指标来源.assets/image-20201215133758797.png)

##### 平台耗卡

![image-20201215134040200](/Users/hanfeng/Documents/workspace/helijiaworkspace/quickbi准实时数据指标来源.assets/image-20201215134040200.png)

##### 店铺耗卡

![image-20201215134153059](/Users/hanfeng/Documents/workspace/helijiaworkspace/quickbi准实时数据指标来源.assets/image-20201215134153059.png)

##### 套餐卡

![image-20201215134226294](/Users/hanfeng/Documents/workspace/helijiaworkspace/quickbi准实时数据指标来源.assets/image-20201215134226294.png)

##### 新优惠卷

![image-20201215134341786](/Users/hanfeng/Documents/workspace/helijiaworkspace/quickbi准实时数据指标来源.assets/image-20201215134341786.png)

##### 会员折扣

<img src="/Users/hanfeng/Documents/workspace/helijiaworkspace/quickbi准实时数据指标来源.assets/image-20201215134419418.png" alt="image-20201215134419418" style="zoom:50%;" />

##### 附加费

![image-20201215134456408](/Users/hanfeng/Documents/workspace/helijiaworkspace/quickbi准实时数据指标来源.assets/image-20201215134456408.png)

##### 平台购买店铺卡耗卡

<img src="/Users/hanfeng/Documents/workspace/helijiaworkspace/quickbi准实时数据指标来源.assets/image-20201215134541438.png" alt="image-20201215134541438" style="zoom:50%;" />



#### 邮件