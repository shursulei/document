### postgresql的yum安装

版本选择pg11，ios(Red hat/Centos7)

安装命令

```shell
#Install the repository RPM:
yum install https://download.postgresql.org/pub/repos/yum/reporpms/EL-7-x86_64/pgdg-redhat-repo-latest.noarch.rpm
#search
yum search postgresql11
#安装客户端
yum install postgresql11
#安装服务器
yum install postgresql11-server postgresql11-contrib
#自启动
/usr/pgsql-11/bin/postgresql-11-setup initdb
systemctl enable postgresql-11
systemctl start postgresql-11
```

数据库实例

创建用户

```shell
groupmod -g 1000 postgres
usermod -u 1000 -g 1000 postgres
```

创建目录并修改权限和所属主

```shell
mkdir -p /pgdata/11/{data,backups,scripts,archive_wals}
chown -R postgres:postgres /pgdata/11/
chmod 0700 /pgdata/11/data/

```

初始化数据目录

```shell
#postgresql用户，输入的密码为postgresql
/usr/pgsql-11/bin/initdb -D /pgdata/11/data -W
##注意/pgdata/11/data下的文件不可以有文件，否在会报错误
```





```shell
#启动数据库
-bash-4.2$ /usr/pgsql-11/bin/postgres -D /pgdata/11/data/ -c port=1922
```





允许远程访问数据库

```shell
##edit 配置文件
echo "host mydb pguser 0.0.0.0/0 md5">> /pgdata/11/data/pg_hba.conf
```

创建test数据库

```shell
-bash-4.2$ psql postgres postgres
psql (11.3)
Type "help" for help.
##-- 创建用户
postgres=# CREATE ROLE pguser WITH ENCRYPTED PASSWORD 'pguser';
CREATE ROLE
##--创建表空间
postgres=# CREATE TABLESPACE tbs_mydb OWNER pguser LOCATION '/database/pg11/pg_tbs/tbs_mydb';
CREATE TABLESPACE

##创建数据库
postgres=# CREATE DATABASE mydb WITH OWNER = pguser TEMPLATE =template0 ENCODING = 'UTF-8' TABLESPACE = tbs_mydb;
CREATE DATABASE

##赋权
postgres=# GRANT ALL ON DATABASE mydb TO pguser WITH GRANT OPTION;
GRANT
postgres=# GRANT ALL ON TABLESPACE tbs_mydb TO pguser;
GRANT

##--创建表空间目录，并修改权限
[root@postgresql ~]# mkdir -p /database/pg11/pg_tbs/tbs_mydb
chown postgres:postgres /database/pg11/pg_tbs/tbs_mydb
```





学习文档:https://github.com/digoal/blog