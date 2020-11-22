# pykafka

# kafka的基本概念

生产着/消费者/消费组/偏移量

重平衡:一个组内的消费者，一旦一个挂了，将一个消费者分区转交给其他消费者

版本的差异:是）0.9.0之前的是consumer_offsets存储在zookeeper上，新的存储在kafka本身上

分区策略和rebalance策略？？

位移：分为kafka自己的位移标志和consumer消费的位移。

位移有如下的概念：上次提交的位移/当前位置/水位/日志最新位移

group coordination protocol组协调者

rebalance:

出现的原因是:当consumer无法在指定的时间内完成消息的处理，那么coordinator就认为consumer已经崩溃了,从而引发新一轮的rebalance。

这里重新rebalance可能包括:consumer崩溃了/组订阅topic数变更了/组订阅topic的分区数变更了。

rebalance分区分配

分区策略

group的leader和coordinator的区别

coordinator是broker级别的



## 解读pykafka源码

1、首先可以拜读一下github和readoc

> https://pykafka.readthedocs.io/en/latest/usage.html

2、接下来开始解析pykafka的源码

代码结构

![image-20201019145627145](pykafka源码解读.assets\image-20201019145627145.png)

## 入门第一步(kafka_tools.py)

初读者可以先解读cli/kafka_tools.py,该脚本是客户端的交互脚本，易对代码有更好的了解

![image-20201019150322359](pykafka源码解读.assets\image-20201019150322359.png)

带有_划线的函数类似于java中的私有的用法,可以由主函数进入

![image-20201019150554225](pykafka源码解读.assets\image-20201019150554225.png)

_get_arg_parser()

#解析获取传入的参数

```
create_topic(client, args):#创建topic
print_managed_consumer_groups(client, args):#获取一个topic的消费组
```