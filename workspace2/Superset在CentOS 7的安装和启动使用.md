说明文档：
     以下是适用于CentOS6和CentOS7


##########################################CentOS 6里将自带的Python2.6.6升级为Python3.6.6.################################################

1、Python版本要求
Superset支持的python版本为Python 2.7 and Python 3.6+. Python 3 是推荐的版本, Python 2.6目前是不支持。

一、查看当前python版本
[hadoop@master app]$
[hadoop@master app]$ python --version
Python 2.6.6
[hadoop@master app]$
[hadoop@master app]$ python2 --version
Python 2.6.6
[hadoop@master app]$


********************************参考安装https://www.cnblogs.com/lwf-blog/p/8182975.html*******************************
CentOS 7 中默认安装了 Python，版本比较低（2.6.6），为了使用新版 3.x，需要对旧版本进行升级。
由于很多基本的命令、软件包都依赖旧版本，比如：yum。
所以，在更新 Python 时，建议不要删除旧版本（新旧版本可以共存）。


二、下载新的python包并安装
首先，将python版本进行给升级
下载
https://www.python.org/ftp/python/3.6.6/Python-3.6.6.tgz
tar xvf Python-3.6.6.tgz
cd Python-3.6.6
./configure
make
make install
即，自动到/usr/local/bin/python3.6（默认路径下）


三、验证
[root@master Python-3.6.6]#
[root@master Python-3.6.6]# python -V
Python 2.6.6
[root@master Python-3.6.6]# python3 -V
Python 3.6.6
[root@master Python-3.6.6]#

等价于
[hadoop@master local]$ /usr/local/bin/python3.6
Python 3.6.6 (default, Apr 13 2019, 10:30:00)
[GCC 4.4.7 20120313 (Red Hat 4.4.7-23)] on linux
Type "help", "copyright", "credits" or "license" for more information.
>>>



四、设置3.X为默认版本
　　查看 Python 的路径，在 /usr/bin 下面。可以看到 python 链接的是 python 2.6，所以，执行 python 就相当于执行 python 2.6。
[root@master Python-3.6.6]# ls -al /usr/bin | grep python
-rwxr-xr-x.   1 root root      11040 Nov 23  2013 abrt-action-analyze-python
-rwxr-xr-x.   2 root root       9032 Nov 22  2013 python
lrwxrwxrwx.   1 root root          6 Apr 12  2017 python2 -> python
-rwxr-xr-x.   2 root root       9032 Nov 22  2013 python2.6
[root@master Python-3.6.6]#


[root@master Python-3.6.6]#
将原来 python 的软链接重命名：
[root@master Python-3.6.6]# mv /usr/bin/python /usr/bin/python.bak

将 python 链接至 python3：
[root@master Python-3.6.6]# ln -s /usr/local/bin/python3 /usr/bin/python
[root@master Python-3.6.6]#



五、配置yum
vim /usr/bin/yum
将#!/usr/bin/python改为#!/usr/bin/python2.6

[root@master Python-3.6.6]# python -V
Python 3.6.6
[root@master Python-3.6.6]#
[root@master Python-3.6.6]#
[root@master Python-3.6.6]# python3 -V
Python 3.6.6
[root@master Python-3.6.6]#

至此，成功！



安装pip3 ：下载安装文件，使用python命令进行安装
说明：pip3(不是pip，pip是python2的)
[root@master bin]# pwd
/usr/bin
[root@master bin]#
[root@master bin]# wget --no-check-certificate https://bootstrap.pypa.io/get-pip.py
则会在/usr/bin目录下，产生得到get-pip.py

[root@master bin]# pwd
/usr/bin
[root@master bin]# python get-pip.py
Traceback (most recent call last):
  File "get-pip.py", line 21361, in <module>
    main()
  File "get-pip.py", line 197, in main
    bootstrap(tmpdir=tmpdir)
  File "get-pip.py", line 82, in bootstrap
    import pip._internal
zipimport.ZipImportError: can't decompress data; zlib not available
[root@master bin]# 

解决办法：
[root@master zlib]# pwd
/home/hadoop/app/Python-3.6.6/Modules/zlib

[root@master Modules]# pwd
/home/hadoop/app/Python-3.6.6/Modules
[root@master Modules]# vim Setup

#zlib zlibmodule.c -I$(prefix)/include -L$(exec_prefix)/lib -lz
去掉注释
zlib zlibmodule.c -I$(prefix)/include -L$(exec_prefix)/lib -lz

[root@master zlib]# ./configure
[root@master zlib]# make
[root@master zlib]# make install


再，
[root@master bin]# pwd
/usr/bin
[root@master bin]# python get-pip.py
[root@master bin]# rm get-pip.py 
rm: remove regular file `get-pip.py'? y
[root@master bin]#

得到
[root@master bin]# pip -V
pip 19.0.3 from /usr/local/lib/python3.6/site-packages/pip (python 3.6)
[root@master bin]#
[root@master bin]#
[root@master bin]# pip3 -V
pip 19.0.3 from /usr/local/lib/python3.6/site-packages/pip (python 3.6)
[root@master bin]#
[root@master bin]# pip3.6 -V
pip 19.0.3 from /usr/local/lib/python3.6/site-packages/pip (python 3.6)
[root@master bin]#


[root@master bin]# ln -s /usr/local/bin/pip3.6 /usr/bin/pip










##########################################Superset在CentOS 7的安装和启动使用################################################

Superset是由Airbnb（知名在线房屋短租公司）开源的数据分析与可视化平台（曾用名Caravel、Panoramix），
该工具主要特点是可自助分析、自定义仪表盘、分析结果可视化（导出）、用户/角色权限控制，还集成了一个SQL编辑器，可以进行SQL编辑查询等，
原来是用于支持Druid的可视化分析，后面发展为支持很多种关系数据库及大数据计算框架，如：mysql, oracle, Postgres,Presto,sqlite, Redshift,Impala, SparkSQL, Greenplum, MSSQL。

说明：此部署方式在服务器可以联网的方式，对于离线安装的话，依赖很多，相对更麻烦。

官方文档：http://superset.apache.org/installation.html（建议用python3.6.X版本）
1、Python版本要求
Superset支持的python版本为Python 2.7 and Python 3.6+. Python 3 是推荐的版本, Python 2.6目前是不支持。

2、环境准备
For Fedora and RHEL-derivatives, the following command will ensure that the required dependencies are installed:
yum install gcc gcc-c++ libffi-devel python3.6-devel python3.6-pip python3.6-wheel openssl-devel libsasl2-devel openldap-devel


（1）安装virtualenv，可以通过pip进行安装
[root@master Python-3.6.6]# pwd
/home/hadoop/app/Python-3.6.6
[root@master Python-3.6.6]# pip install virtualenv
Collecting virtualenv
  Downloading https://files.pythonhosted.org/packages/33/5d/314c760d4204f64e4a968275182b7751bd5c3249094757b39ba987dcfb5a/virtualenv-16.4.3-py2.py3-none-any.whl (2.0MB)
    100% |████████████████████████████████| 2.0MB 98kB/s
Installing collected packages: virtualenv
Successfully installed virtualenv-16.4.3
[root@master Python-3.6.6]#

（2）创建虚拟环境
ot@master Python-3.6.6]# python3 -m venv venv
[root@master Python-3.6.6]# pwd
/home/hadoop/app/Python-3.6.6
会在当前目录，生成venu目录

（3）激活虚拟环境
[root@master Python-3.6.6]# . venv/bin/activate
(venv) [root@master Python-3.6.6]#



3、在虚拟环境venv里安装superset
（1）在虚拟环境里，进行
(venv) [root@master Python-3.6.6]# pip install --upgrade setuptools pip     #升级setuptools和pip
(venv) [root@master Python-3.6.6]# pip install superset  #安装superset
对应，保存在/home/hadoop/app/Python-3.6.6/venv/lib/python3.6/site-packages


（2）安装superset数据库包
(venv) [root@master Python-3.6.6]# pip install superset
执行完后，会生成...
找到/home/hadoop/app/Python-3.6.6/venv/lib/python3.6/site-packages/superset

(venv) [root@master Python-3.6.6]# pip install mysqlclient

(venv) [root@master Python-3.6.6]#  pip install pymysql

(venv) [root@master Python-3.6.6]# pip install pymssql


（3）配置好superset需要的元数据库
[root@master superset]# pwd
/home/hadoop/app/Python-3.6.6/venv/lib/python3.6/site-packages/superset

[root@master superset]# vim config.py
SQLALCHEMY_DATABASE_URI = 'mysql://root:root@192.168.80.145:3306/superset?charset-utf8'
默认是sqlite，一般用mysql来作为元数据库。当然更多见https://blog.csdn.net/jin6872115/article/details/83548846和http://www.woshipm.com/data-analysis/760397.html
更可以，见官网的http://superset.apache.org/installation.html#getting-started

SQLALCHEMY_DATABASE_URI = 'sqlite:///' + os.path.join(DATA_DIR, 'superset.db')

如果出现ModuleNotFoundError: No module named '_sqlite3'
解决办法：
1 安装 sqlite-devel
yum install sqlite-devel

2 重新编译python
[root@master Python-3.6.6]# pwd
/home/hadoop/app/Python-3.6.6
[root@master Python-3.6.6]#
./configure
make
make install


则会在当前用户的家目录，我的是/root下生成.superset/superset.db
[root@master ~]# cd .superset/
[root@master .superset]# pwd
/root/.superset
[root@master .superset]# ll
total 104
-rw-r--r-- 1 root root 101376 Apr 13 17:31 superset.db
[root@master .superset]#






这里如果不设置数据库为utf8，在后面初始化数据库时会报  Specified key was too long; max key length is 767 bytes 的错误
mysql> alter database superset character set utf8;


（4）初始化superset，并建立好管理员账号
[root@master Python-3.6.6]# pwd
/home/hadoop/app/Python-3.6.6
[root@master Python-3.6.6]# . venv/bin/activate
(venv) [root@master Python-3.6.6]# fabmanager create-admin --app superset
Username [admin]: admin
User first name [admin]: admin
User last name [user]: admin
Email [admin@fab.org]: lszhou2013@163.com
Password: admin
Repeat for confirmation: admin
Was unable to import superset Error: No module named 'MySQLdb'
(venv) [root@master Python-3.6.6]#

分析：
MySQLdb只支持Python2.*，还不支持3.*.可以用PyMySQL代替
目前python2和python3在数据库模块支持这里存在区别，python2是mysqldb，而到了python3就变成mysqlclient，pip install mysqlclient即可。


解决办法：
pymysql是安装Python中的MySQL驱动程序
(venv) [root@master Python-3.6.6]#  pip install pymysql
(venv) [root@master mysql]# pwd
/home/hadoop/app/Python-3.6.6/venv/lib/python3.6/site-packages/sqlalchemy/dialects/mysql
(venv) [root@master mysql]# vim __init__.py

在
# default dialect
base.dialect = mysqldb.dialect
后的下一行，增加
import pymysql
pymysql.install_as_MySQLdb()

然后，继续尝试
Was unable to import superset Error: cannot import name '_maybe_box_datetimelike'
则是因为pandas版本太高了
(venv) [root@master Python-3.6.6]# pip uninstall pandas
(venv) [root@master Python-3.6.6]# pip install pandas==0.23.4


(venv) [root@master Python-3.6.6]#
(venv) [root@master Python-3.6.6]# fabmanager create-admin --app superset
Username [admin]: admin
User first name [admin]: admin
User last name [user]: admin
Email [admin@fab.org]: lszhou2013@163.com
Password:
Repeat for confirmation:
Recognized Database Authentications.
Admin User admin created.
(venv) [root@master Python-3.6.6]#




（5）# Initialize the database
(venv) [root@master Python-3.6.6]# superset db upgrade

如果在这一步，出现
sqlalchemy.exc.InternalError: (pymysql.err.InternalError) (1071, 'Specified key was too long; max key length is 1000 bytes')
[SQL: ALTER TABLE datasources ADD CONSTRAINT uq_datasources_cluster_name UNIQUE (cluster_name, datasource_name)]
(Background on this error at: http://sqlalche.me/e/2j85)

解决办法：
则在https://github.com/apache/incubator-superset/issues/4691
I encountered the same issue, downgrading sqlalchemy version to 1.2 fixed this for me. i hope it helps.
(venv) [root@master Python-3.6.6]# pip uninstall sqlalchemy
(venv) [root@master Python-3.6.6]# pip install sqlalchemy==1.2.2



（6）# Load some data to play with
(venv) [root@master Python-3.6.6]# superset load_examples   #导入案例


（7）# Create default roles and permissions
(venv) [root@master Python-3.6.6]# superset init    #初始化superset数据库


（8）(venv) [root@master Python-3.6.6]# superset runserver -d    #后台启动
 我这里，如在使用中，考虑到yarn的端口也在8088，所以可以更改端口
如(venv) [root@master Python-3.6.6]# superset runserver -d 8089