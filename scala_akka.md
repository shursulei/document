scala的使用场景

scala基于jvm的脚本化语言，

scala和java的区别优缺点:

scala是完全面向对象的写法

scala的优势



AnyRef->Object

## 特质(trait)：

代码复用的基础单元

特质无类参数

特质和抽象类的区别:

特质是另一种类，在JVM层，被解析成接口，所以性能这一块被类要差；但是作为一种类的行为，扩展了类，将廋接口变成胖接口

## 样本类和模式匹配

option类

## scala与java交互:

JavaConversions



# akka

akka对象创建的方式不同:使用的是actorOf，返回一个actorRef

### Actor 的生命周期

Actor 在被创建时就会出现，然后在用户请求时被停止。每当一个 Actor 被停止时，它的所有子 Actor 也会被递归地停止。这种行为大大简化了资源清理，并有助于避免诸如由打开的套接字和文件引起的资源泄漏。

akka的版本不允许混用，选择最新的版本，不然会报错

```
You are using version 2.6.6 of Akka, but it appears you (perhaps indirectly) also depend on older versions
of related artifacts. You can solve this by adding an explicit dependency on version 2.6.6 of the
[akka-persistence-query] artifacts to your project. 
See also: https://doc.akka.io/docs/akka/current/common/binary-compatibility-rules.html#mixed-versioning-is-not-allowed
```



Actor 是封装状态和行为的对象，它们通过交换放在收件人邮箱中的消息进行专门的通信。

`ActorSystem`是一个重量级架构，它将分配`1 … N`个线程，因此为每个逻辑应用程序都创建一个线程。



Akka 中的生命周期监控通常被称为`DeathWatch`。