spre的结束界面：

##任何用户都可以运行sas代码

cd /opt/sas/spre/home/SASFoundation

./sas

endsas;





### 使用viya界面读取环境变量

```SAS
%put %sysget(SAS_HADOOP_CONFIG_PATH)

data getenv;
homedir=sysget('HADOOP_HOME');
put homedir=;
javahome=sysget('JAVA_HOME');
put javahome=;
run;
```





1、此例子只能清洗在字段末尾出现半个中文字符的情况

data a; 

length x $8; 

 x="a测试测试"; 

 put x=; 

 run; 

  

 data _null_; 

  set a; 

  l=klength(x);/* 取回中文字符数，一个中文字长度是1，一个英文字母也是1，不能识别的半个字符不算 */ 

  put l=; 

  x1=ksubstr(x,1,l);/* 按照可识别长度截取字符值，如果后面有半个字符，将被截掉 */ 

  put x=; 

  if length(x) ne length(x1) then x=x1;/* 如果截取后的字符值长度，不等于原始长度，则替换原值 */ 

  put "After transform: " x= ; 

  drop l x1; 

run; 

 

\2. 清理字符类型中不可见字符的情况

data 目标表;

set 要转的表;

array cc (*) _character_;

do _N_=1 to dim(cc);

cc(_N_)=kpropdata(cc(_N_));

end;

run;





swap空间vm.swappiness=1







### 查看数据类型和数据结构

```SAS
proc contents data=library.xx;
run;
porc contents data=x.b;run;
/*第二种*/
ods trace on;
proc contents data=TableA;
   ods output Variables=need;
run;
ods trace off;

```

proc javainfo all;run;



### 查看本地的编码格式和编码方式

```SAS
proc options option=local;
run;
proc options option=encoding;
run;
```

### 查看产品组件和license

```sas
proc setinit;run;
```

### data 步 造数据

```SAS
data work.bigcars;
set sashelp.cars;
do  i =1 to 1000;
output;
end;
run;
```

### sas查看表大小

```SAS
proc sql noprint;
    select filesize into :size
        from DICTIONARY.TABLES
            where libname='SASHELP' and memname='CLASS';
quit;
%put NOTE-&size bytes;
```

### sas查看表的行数和列数

```SAS
data test;
set sashelp.class;
run;
*行数 nobs;
proc sql;
select nobs into :nobs
from dictionary.tables
where libname="WORK" and memname="TEST";
quit;
%put &nobs;
*列数 nvar;
proc sql;
select nvar into :nvars
from dictionary.tables
where libname="WORK" and memname="TEST";
quit;
%put &nvars;
```

### access to jdbc连接oracle的date和clob特殊类型的数据

```sas
//sas的oracle对clob和date的数据类型的转换
data work.test2
set x.async_args_list_info(dbstype=(a='date',b='date',c='char(20)'));
run;
```





```SAS
filename x pipe 'klist';
data _null_;
infile x;
input;
put _infile_;
run;
```

### 输出完整日志和产品版本号到控制台

```SAS
%put Site: &syssite    Release: &sysvlong    System: &sysscp &sysscpl;    %put &sysuserid;
options sastrace='d' nostsuffix sastraceloc=saslog;
libname x JDBC driverclass="org.postgresql.Driver" URL="jdbc:postgresql://122.21.173.20:25308/icbc_edw_dev" user=mppdew schema=public
password="mppdew@123"
classpath="/datafs/sas94/thirdpart/mppdb/gsjdbc4.jar"
Proc print data=x.TEST_SAS_ZH;
run;
proc sql;
insert into x.TEST_SAS_ZH(name,sex) values('测试','mm');
quit;  
```

### java查看版本oracle的版本信息

```shell
/sas/home/SASPrivateJavaRuntimeEnvironment/9.4/jre/bin/java -jar /sas/thirdparty/ojdbc7.jar -version
```

### 打印完整日志

```SAS
options sastrace="d," sastraceloc=saslog;
options sastrace=",,,d a" stsuffix sastraceloc=saslog;
options sastrace="d,,d,d" stsuffix sastraceloc=saslog;
```

### 加密

```SAS
proc pwencode in='Oper1234';
run;
```

### 查看宏变量

```SAS
%put _automatic_;
```

### 查看逻辑库信息

```SAS
proc contents data=mpdtnw._all_ out=temp;run;
proc sql;
create table sas_table as select * from temp;
quit;
```

### pipe

```SAS
filename x pipe "ls /";
data _null_;
infile x;
input;
put _infile_;
run;
```

jdbc-mppdb的utf8

```
sas_env的文件中
export PGCLIENTENCODING=utf8

```

SAS/SHARE服务器

```sas
proc operate serverid=__3124;
```

### 验证SASAPP是否在工作

```sas
 /* Standard SASApp Workspace server status check */
proc iomoperate ; connect hostname="<machine>" port=8591 username="someadminaccount@saspw" password="guesswhat";
list information;
quit;
使用ValidateServer验证
# Validate Logical Workspace Server

/opt/sas94/sashome/SASPlatformObjectFramework/9.4/ValidateServer -msp "host=localhost port=8561 domain=DefaultAuth userid=myuserid password={sas002}MYPWENCODEDPASS reposid=A0000001.A9ZZZZZ9" -sn "SASApp - Logical Workspace Server" -cred "domain=DefaultAuth userid=myuserid password={sas002}MYPWENCODEDPASS"; echo $?

https://communities.sas.com/t5/Administration-and-Deployment/Script-Server-Validation/td-p/292369
```

```shell
script /tmp/sas_info.txt 

cd $HOME 

pwd 

cd /u02/SASConfig/Lev1/SASApp/WorkspaceServer 

sh -x ./WorkspaceServer.sh -nodms -rtrace ALL -verbose > /tmp/sas_info2.txt 2>&1 
```

### sas列出sas session中的所有信息

```shell
sh -x ./WorkspaceServer.sh -nodms -rtrace ALL -verbose > /tmp/sas_info2.txt 2>&1 
```



### 查看使用的用户id

```SAS
%put &sysuserid;
```









