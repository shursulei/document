1、修改/datafs/sas94/configapp/Lev1/SASApp/WorkSpaceServer

sasv9_root.cfg 的-JREOPTIONS(-Dfile.encoding=UTF-8)

2、使用sas_en,sas_u8,sas_zh的方式使用

3、dbconinit="set client_encoding to 'latin1'"





oracle



```SAS
libname x JDBC driverclass="oracle.jdbc.driver.OracleDriver" URL="jdbc:oracle:thin:@//122.18.125.158:1521/DAS" 
user="DAS" schema="DAS" password="DAS" classpath="/datafs/sas94/thirdpart/oracle/ojdbc5.jar"

data x.ml_class;
set sashelp.class;
run;
报错信息//ERROR: 试图创建DBMS表时出错。ERROR：执行出错:ORA-00906:缺失左括号
proc sql;
create table x.ml_test1
(
aa varchar(30),bb varchar(40),cc char(1)
);
quit;
//ERROR: 试图创建DBMS表时出错。ERROR：执行出错:ORA-00906:缺失左括号
```



mppdb

```SAS
libname x JDBC driverclass="org.postgresql.Driver" URL="jdbc:postgresql://122.21.173.20:25308/icbc_edw_dev" user=mppdew schema=public
password="mppdew@123"
classpath="/datafs/sas94/thirdpart/mppdb/gsjdbc4.jar"
proc sql;
insert into x.TEST_SAS_ZH(name,sex) values('测试','mm');
quit;
proc print data=x.TEST_SAS_ZH2;
run;

FunsionInsight LibrA
```





```SAS
libname x JDBC driverclass="org.postgresql.Driver" URL="jdbc:postgresql://122.21.173.20:25308/icbc_edw_dev" user=mppdew schema=public
password="mppdew@123"
classpath="/datafs/sas94/thirdpart/mppdb/gsjdbc4.jar"
data work.bigcars;
set sashelp.cars;
do i =1 to 2000;
output;
end;
run;
data x.ml_bigcars2000;
set work.bigcars;
run;
//实际时间 ： 56:25.77
//CPU时间 ： 31：23.32

libname x postgresql dsn="GaussODBC" user="mppedw" password="mppedw@123";
data work.bigcars;
set sashelp.cars;
do i =1 to 2000;
output;
end;
run;
data x.ml_bigcars20000;
set work.bigcars;
run;
//实际时间: 15:42.18
//cpu时间: 1:04.52
```

