python调度

循环的使用



# [python中的__new__方法](https://www.cnblogs.com/chvv/p/9950837.html)



```

1.创建类时先执行type的__init__方法,
2.当一个类实例化时(创建一个对象)执行type的__call__方法，__call__方法的返回值就是实例化的对象
　　　　__call__内部调用
　　　　　　-类.__new__方法，创建一个对象
　　　　　　-类.__init__方法，初始化对象
实例化对象是谁取决于__new__方法,__new__返回什么就是什么

　__new__() 方法的特性：
```

- **__new__() 方法是在类准备将自身实例化时调用。**
- **__new__() 方法始终都是类的静态方法，即使没有被加上静态方法装饰器**

<https://www.cnblogs.com/chvv/p/9950837.html>



<https://www.cnblogs.com/niusha/p/10618493.html>