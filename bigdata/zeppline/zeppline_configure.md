实验版本zeppline0.8



## Generic JDBC Interpreter 



### JDBC-To-Mysql

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
#远程授权
GRANT ALL PRIVILEGES ON *.* TO 'root'@'%' IDENTIFIED BY 'Mysql@123456' WITH GRANT OPTION;
#刷新使生效
FLUSH PRIVILEGES;
```

##### 1.1.5 设置自动启动

```shell
systemctl enable mysqld
systemctl daemon-reload
```

#### 1.2 config-mysql-zeppline

  参考官方文档的配置

​	需要自己下载数据库对应的包





## notebook 存储在mongodb

#### 1.Install of Mongodb

#### 1.1配置MongoDB的yum源



## notebook Authorization

### 1.Apache Shiro authentication for Apache Zeppelin

1.1配置shiro

```shell
cp conf/shiro.ini.template conf/shiro.ini
```

