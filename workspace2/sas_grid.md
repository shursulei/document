

```shell
cd /sas/sas94/SASHome/SASFoundation/9.4/;
mv sas sasbak;
ln -s bin/sas_u8 sas
#/sas/sas94/Config/Lev1/level_env.sh
```

验证方式:

1、EG查看系统会话编码，已经变成了UTF-8

```SAS
proc options option=encoding;
run;
/*查看会话编码, ENCODING=UTF-8    指定 SAS 会话的默认字符集编码。*/
```

2、SQL查询验证





3、将 clispb.dat拷贝到共享目录/nfs下，准备分发。

```shell
cp /opt/teradata/client/15.00/lib/clispb.dat /nfs/
echo "10.134.13.170
10.134.13.171
10.134.13.172
10.134.13.173
10.134.13.174
10.134.13.175
10.134.13.176
10.134.13.177">>/nfs/apphots
```

4、root用户登录到元数据服务器10.129.42.203（该元数据服务器有免密登录的功能），开始分发

```shell
for ip in `cat /nfs/apphosts`;do ssh $ip 'cp /nfs/clispb.dat /opt/teradata/client/15.00/lib/';done;
```

5、验证配置文件

```shell
for ip in `cat /nfs/apphosts`;do ssh $ip 'ls /opt/teradata/client/15.00/lib/clispb.dat';done;
for ip in `cat /nfs/apphosts`;do ssh $ip 'cat /opt/teradata/client/15.00/lib/clispb.dat';done;
```

