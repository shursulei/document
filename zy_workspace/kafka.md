kafka单机安装

1、安装配置

```
kafka_2.12-2.3.0
server.properties 
#打开注释
listeners=PLAINTEXT://:9092
advertised.listeners=PLAINTEXT://your.host.name:9092
```

启动/创建topic



python客户端

> https://kafka-python.readthedocs.io/en/master/
>
> https://pypi.org/project/pykafka/



pykafka和kafka-python的区别

pykafka的性能优先级较高

区别 https://github.com/Parsely/pykafka/issues/334

https://pypi.org/project/pykafka/

```
pip install kafka-python
pip install pykafka
```

```
./kafka-run-class.sh kafka.tools.DumpLogSegments --files /kafka/kafka-logs-dda85f8c6e51/prescription-0/00000000000000000000.log --print-data-log
```



pykafka的源码