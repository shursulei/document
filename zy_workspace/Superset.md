



anconda 3.8



Superset 安装



http://superset.apache.org/installation.html



```python
#conda create -n superset python=3.7
# Install superset
pip install apache-superset

# Initialize the database
superset db upgrade

# Create an admin user (you will be prompted to set a username, first and last name before setting a password)
$ export FLASK_APP=superset
superset fab create-admin
#superset 账号密码
#superset@163.com 邮箱

# Load some data to play with
superset load_examples

# Create default roles and permissions
superset init

# To start a development web server on port 8088, use -p to bind to another port
superset run -p 8088 --with-threads --reload --debugger

#安装客户端 mysql
apt-get install libmysqlclient-dev 
pip install mysqlclient
```





配置

```
mysql+mysqldb://sulei:XXXXXXXXXX@172.16.50.187:3306/ih
```



生产建议安装的方式、



```
https://zhuanlan.zhihu.com/p/29269918
https://superset.incubator.apache.org/installation.html#configuration
```



趟过的坑

```
#superset 没有返回结果
superset Results backend isn't configured
```

