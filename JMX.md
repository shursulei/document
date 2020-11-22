JMX作为JAVA程序的监控插件

![img](https://i.loli.net/2018/05/27/5b0ab8db177a1.jpg)

$JAVA_HOME/bin/jvisualvm.exe

java程序开启

```shell
-Djava.rmi.server.hostname=127.0.0.1 
-Dcom.sun.management.jmxremote.port=1000 
-Dcom.sun.management.jmxremote.ssl=false 
-Dcom.sun.management.jmxremote.authenticate=false
```



参考文章

```
https://www.jianshu.com/p/fa4e88f95631
https://www.cnblogs.com/trust-freedom/p/6842332.html
https://www.imooc.com/article/37008?block_id=tuijian_wz
Spring 结合JMX
http://ifeve.com/%E3%80%8Aspring-5%E5%AE%98%E6%96%B9%E6%96%87%E6%A1%A3%E3%80%8B-jmx/
```





HDFS的JMX



```
DataNodeMXBean
DataNode
MBeanUtil
FSDatasetMBean
FSNamesystemMBean
```