因为最近在开始学习netty，本人喜欢将demo和源码以及书一起结合起来学习，所以平时自己会总结一些知识。

编解码器

```
ReplayingDecoder
```

```java
LineBasedFrameDecoder
```

HttpObjectDecoder 





编码器

MessageToByteEncoder 

WebSocket08FrameEncoder 

```
MessageToMessageEncoder
MessageToMessageCodec


http编码和解码
HttpRequestEncoder
HttpRequestDecoder
```

最近因工作的需求，开始学习和研究netty，以下都是我个人的理解，有错误的希望能够指正。

首先介绍netty,netty

术语给出的介绍：Netty 是一款异步的事件驱动的网络应用程序框架，支持快速地开发可维护的高性能的面向协议的服务器和客户端。 

个人的学习方式是：以网上教程+源代码+实际代码+书籍方式。

个人感觉netty作为一种简单的工具，利用与大数据行业，应用程序之间的通信等相关问题等。

在网上都有涉及到相关概念，比如javaNIO,socket，选择器，阻塞I/O和非阻塞I/O的区别，可以自行百度和了解。

Netty的核心组件包含channel、回调



netty的参考API地址
https://netty.io/4.1/api/index.html





打包：https://jingyan.baidu.com/album/647f0115f8af407f2148a8c8.html?picindex=1



netty学习博客文章:

https://www.jianshu.com/p/b9f3f6a16911

https://www.jianshu.com/p/ed0177a9b2e3

https://waylau.gitbooks.io/essential-netty-in-action/content/CORE%20FUNCTIONS/Buffers.html

http://ifeve.com/category/netty/



https://blog.csdn.net/LIAN_XL/article/details/79799072