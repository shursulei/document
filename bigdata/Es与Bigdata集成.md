# Es与Bigdata集成

## 硬件要求

内存、CPU、

磁盘：SSD针对的是数据节点；其他节点的配置可以更换

网络：最好不要跨数据中心

## 集群规划

![1559636674538](D:\Typora\workspace\bigdata\集群规划.png)

名称规划

## Elasticsearch集群环境部署

1、节点规划

|   节点   | 192.168.145.101 | 192.168.145.102 | 192.168.145.103 | 192.168.145.104 | x.x.145.105 |
| :------: | --------------- | --------------- | --------------- | --------------- | ----------- |
|  master  | true            | false           | false           | false           | false       |
| 负载均衡 | false           | true            | false           | false           | false       |
|   data   | false           | false           | true            | false           | false       |
|   data   | false           | false           | false           | true            | false       |
|   data   | false           | false           | false           | false           | true        |

2、用户

​	创建用户:eshadoop

```shell
groupadd hadoop
```

<https://blog.csdn.net/sinat_37690778/article/details/78903451>

## Es-hadoop环境部署（1.0）

前提



一、基础配置

1.1、java配置

1.2、ssh配置

1.3、用户分配

二、hadoop安装配置

三、Elasticsearch安装配置



## 与Spark集成

