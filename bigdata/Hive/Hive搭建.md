#### 1.1 Install of Mysql

此mysql用于测试版本

##### 1.1.2 下载mysql源安装包

```shell
yum install wget
wget http://dev.mysql.com/get/mysql57-community-release-el7-11.noarch.rpm
yum -y install mysql57-community-release-el7-11.noarch.rpm
##查看安装效果
yum repolist enabled | grep mysql.*
```

##### 1.1.3 安装mysql服务器并启动

```shell
yum install mysql-community-server
systemctl start  mysqld.service
systemctl status mysqld.service
```

##### 1.1.4 初始化数据库密码

```shell
#查看初始密码
grep "password" /var/log/mysqld.log
(base) [root@zeppelin opt]# grep "password" /var/log/mysqld.log 
2019-06-17T00:14:34.180194Z 1 [Note] A temporary password is generated for root@localhost: hH3YPnTrt9=s
#登录
mysql -uroot -p
##密码
#修改密码
ALTER USER 'root'@'localhost' IDENTIFIED BY 'Mysql@123456';
grant all privileges on *.* to 'root'@'%' identified by 'Mysql@123456' with grant option;
#远程授权
GRANT ALL PRIVILEGES ON *.* TO '*'@'%' IDENTIFIED BY 'Mysql@123456' WITH GRANT OPTION;
#刷新使生效
FLUSH PRIVILEGES;
```

##### 1.1.5 设置自动启动

```shell
systemctl enable mysqld
systemctl daemon-reload
```

#### 1.2创建用户设置密码



```shell
[root@SecondNamenode app]# useradd hive -d /home/hive -m -u 1402
[root@SecondNamenode app]# echo "hive:hive" |chpasswd 


ln -s /app/apache-hive-1.2.2-bin /opt/hive

```

<https://www.cnblogs.com/tq03/p/5107949.html>



<https://blog.csdn.net/yangang1223/article/details/80183038>





