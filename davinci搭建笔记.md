参考：https://blog.csdn.net/maxi1234/article/details/103589992
https://blog.csdn.net/weixin_43215250/article/details/89636548?depth_1-utm_source=distribute.pc_relevant.none-task-blog-BlogCommendFromBaidu-5&utm_source=distribute.pc_relevant.none-task-blog-BlogCommendFromBaidu-5


	使用davinci可以快速将多种数据源中的数据以柱状图、折线图、饼图等数据可视化展示。使开发者在不会前端、不使用任何报表工具的情况下快速可视化数据。
	
	官网地址
	https://edp963.github.io/davinci/
	
	GitHub地址
	https://github.com/edp963/davinci


​	
支持的数据源
​	数据源名称			驱动类
​	mysql				com.mysql.jdbc.Driver
​	oracle				oracle.jdbc.driver.OracleDriver
​	sqlserver			com.microsoft.sqlserver.jdbc.SQLServerDriver
​	h2					org.h2.Driver
​	phoenix				org.apache.phoenix.jdbc.PhoenixDriver
​	mongodb				mongodb.jdbc.MongoDriver
​	elasticSearch		–
​	presto				com.facebook.presto.jdbc.PrestoDriver
​	moonbox				moonbox.jdbc.MbDriver
​	cassandra			com.github.adejanovski.cassandra.jdbc.CassandraDriver
​	clickhouse			ru.yandex.clickhouse.ClickHouseDriver
​	kylin				org.apache.kylin.jdbc.Driver
​	vertica				com.vertica.jdbc.Driver
​	hana				com.sap.db.jdbc.Driver
​	impala				com.cloudera.impala.jdbc41.Driver


​	
​	
安装
​	参考https://edp963.github.io/davinci/docs/zh/1.1-deployment
​	

下载
	见https://github.com/edp963/davinci/releases/download/v0.3.0-beta.4/davinci-assembly_3.0.1-0.3.0-SNAPSHOT-dist-beta.4.zip


​	
[hadoop@master davinci]	
unzip davinci-assembly_3.0.1-0.3.0-SNAPSHOT-dist.zip
​	
​	
[hadoop@master bin] cd config
[hadoop@master config] cp application.yml.example application.yml 


[hadoop@master bin]$ cat /etc/profile
#davinci
export DAVINCI3_HOME=/home/hadoop/app/davinci
export PATH=$PATH:$DAVINCI_HOME/bin



则在mysql客户端里，执行SET GLOBAL log_bin_trust_function_creators = 1;
[hadoop@master bin]$ chmod 755 initdb.sh 
[hadoop@master bin]$ sh ./initdb.sh 


如果需重新删除，则
drop table dashboard;               
drop table dashboard_portal;        
drop table display;                 
drop table display_slide;           
drop table favorite;                
drop table mem_dashboard_widget;    
drop table mem_display_slide_widget;
drop table organization;            
drop table project   ;              
drop table rel_team_project;        
drop table rel_user_organization ;  
drop table rel_user_team      ;     
drop table source         ;         
drop table star        ;            
drop table team       ;             
drop table user       ;             
drop table view       ;             
drop table widget    ;              






需安装redis，作为davinci的缓存
https://blog.csdn.net/wawawawawawaa/article/details/81431841


系统默认的管理员账号和密码分别是，super@davinci.com / 123456。



http://192.168.80.145:8090/