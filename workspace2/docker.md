# docker-cdh

下载安装docker,配置阿里云加速镜像



配置阿里云加速镜像

```
[root@localhost ~]# cat /etc/docker/daemon.json
{
"registry-mirrors":["https://2elq31uu.mirror.aliyuncs.com"]
}

```

重启daemon

```
systemctl daemon-reload
```

重启docker服务

```
systemctl restart docker
```

下载cloudera

```
docker pull cloudera/quickstart:latest
```

启动

```shell
docker run --name cdh --hostname=quickstart.cloudera --privileged=true -t -i -p 8020:8020 -p 8022:8022 -p 7180:7180 -p 21050:21050 -p 50070:50070 -p 50075:50075 -p 50010:50010 -p 50020:50020 -p 8890:8890 -p 60010:60010 -p 10002:10002 -p 25010:25010 -p 25020:25020 -p 18088:18088 -p 8088:8088 -p 19888:19888 -p 7187:7187 -p 11000:11000 cloudera/quickstart /bin/bash -c '/usr/bin/docker-quickstart && /home/cloudera/cloudera-manager --express && service ntpd start'
```



进入容器

```shell
docker exec -it 775c7c9ee1e1 /bin/bash 
```

容器同步时间

```shell
vim /etc/ntp.conf
# 将时钟服务器更改为如下4个
server 0.cn.pool.ntp.org
server 1.cn.pool.ntp.org
server 2.cn.pool.ntp.org	
server 3.cn.pool.ntp.org
```

重启NTP服务

```shell
service ntpd start \
&& ntpdate -u 0.cn.pool.ntp.org \
&& hwclock --systohc \
&& date
```



# docker-mysql

```shell
docker run -p 3306:3306 --name mymysql -v $PWD/conf:/etc/mysql/conf.d -v $PWD/logs:/logs -v $PWD/data:/var/lib/mysql -e MYSQL_ROOT_PASSWORD=123456-d mysql
docker run --name mysql -p 3306:3306 -e MYSQL_ROOT_PASSWORD=123456 -d mysql
```



# docker-openldap

### docker安装openldap

```
docker run  --env LDAP_ORGANISATION="embrace" --env LDAP_DOMAIN="embrace.com" --env LDAP_ADMIN_PASSWORD="123" -p 389:389 -p 636:636  --name my-openldap-container --detach osixia/openldap:latest

docker exec  my-openldap-container ldapsearch -x -H ldap://localhost -b dc=embrace,dc=com -D "cn=admin,dc=embrace,dc=com" -w 123
 
```

### docker 安装phpadmin



```
docker run -d --privileged -p 10004:80 --name myphpldapadmin --env PHPLDAPADMIN_HTTPS=false --env PHPLDAPADMIN_LDAP_HOSTS=172.17.0.2  --detach osixia/phpldapadmin
```

http://localhost:10004

user:cd=admin,dc=embrace,dc=com

password=123





# docker 安装superset

```shell
docker search superset
docker pull amancevice/superset
mkdir -p  /data/docker/volumes/superset/conf
mkdir -p /data/docker/volumes/superset/data
docker run -p 8088:8088 -v /data/docker/volumes/superset/conf:/etc/superset -v /data/docker/volumes/superset/data:/data --name superset -d amancevice/superset

docker run -p 8089:8089 -v /data/docker/volumes/superset/conf:/etc/superset -v /data/docker/volumes/superset/data:/data --name superset2 -d amancevice/superset

```



# docker-kafka

做端口转发

```shell
docker pull wurstmeister/zookeeper;
docker run -d --restart=always --log-driver json-file --log-opt max-size=100m --log-opt max-file=2  --name zookeeper -p 2181:2181 -v /etc/localtime:/etc/localtime wurstmeister/zookeeper;

docker pull wurstmeister/kafka;
docker run -d --restart=always --log-driver json-file --log-opt max-size=100m --log-opt max-file=2 --name kafka -p 9092:9092 -e KAFKA_BROKER_ID=0 -e KAFKA_ZOOKEEPER_CONNECT=172.20.146.107:2181/kafka -e KAFKA_ADVERTISED_LISTENERS=PLAINTEXT://172.20.146.107:9092 -e KAFKA_LISTENERS=PLAINTEXT://0.0.0.0:9092 -v /etc/localtime:/etc/localtime wurstmeister/kafka;
```



## docker安装/启动kafka,zookeeper

```shell
docker pull wurstmeister/zookeeper:3.5.6
docker pull wurstmeister/kafka:2.12-2.3.0
#
docker inspect zookeeper#获取ip
##
docker run -d --name zookeeper --publish 2181:2181 \
--volume /etc/localtime:/etc/localtime \
zookeeper:3.5.6

docker run -d --name kafka --publish 9092:9092 \
--link zookeeper \
--env KAFKA_ZOOKEEPER_CONNECT=zookeeper:2181 \
--env KAFKA_ADVERTISED_HOST_NAME=172.17.0.6 \
--env KAFKA_ADVERTISED_PORT=9092 \
--volume /etc/localtime:/etc/localtime \
-t wurstmeister/kafka:2.12-2.3.0
```

docker-kafka集群的搭建方式:

https://www.cnblogs.com/once/p/10146666.html

```shell
./kafka-topics.sh --zookeeper 172.17.0.5:2181/kafka-cluster --create --topic my-topic --replication-factor 2 --partitions 8
./kafka-topics.sh --zookeeper 172.17.0.5:2181/kafka-cluster --create --topic my-topic --replication-factor 1 --partitions 8
```

```shell
./kafka-topics.sh --bootstrap-server localhost:9092 --create --topic my-topic --partitions 1 --replication-factor 1
./kafka-topics.sh --bootstrap-server localhost:9092 --list
./kafka-topics.sh --bootstrap-server localhost:9092 --describe
```

kafka分区奇数？？？？



## python-kafka

<https://blog.csdn.net/feng973/article/details/78814291>



```python
import time
from kafka import KafkaProducer
producer = KafkaProducer(bootstrap_servers = ['192.168.17.64:9092', '192.168.17.65:9092', '192.168.17.68:9092'])
# Assign a topic
topic = 'test'
def test():
    print('begin')
    n = 1
    try:
        while (n<=100):
            producer.send(topic, str(n).encode())
            print "send" + str(n)
            n += 1
            time.sleep(0.5)
    except KafkaError as e:
        print e
    finally: 
        producer.close()
        print 'done'



def test_json():
	msg_dict = {
		"sleep_time": 10,
		"db_config": {
			"database": "test_1",
			"host": "xxxx",
			"user": "root",
			"password": "root"
		},
		"table": "msg",
		"msg": "Hello World"
	}
	msg = json.dumps(msg_dict)
	producer.send(topic, msg, partition=0)
	producer.close()


if __name__ == '__main__':
    test()

```



```python
from kafka import KafkaProducer
import json
producer = KafkaProducer(bootstrap_servers = ['localhost'])
topic = 'my-topic'
def test_json():
	msg_dict = {
		"sleep_time": 10,
		"db_config": {
			"database": "test_1",
			"host": "xxxx",
			"user": "root",
			"password": "root"
		},
		"table": "msg",
		"msg": "Hello World"
	}
	msg = json.dumps(msg_dict)
	producer.send(topic, msg, partition=0)
	producer.close()
if __name__ == '__main__':
    test()
```



```shell
cd /opt/kafka/bin/
./kafka-run-class.sh kafka.tools.DumpLogSegments --files /kafka/kafka-logs-78dc7f377ec7/my-topic-0/00000000000000000000.log --print-data-log > 00000000000000000000.txt
```



docker-spark

```
docker run -t -i sequenceiq/spark /bin/bash
```

