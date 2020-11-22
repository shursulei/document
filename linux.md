# NSCD

<https://www.jianshu.com/p/a0a81062cdb4>



<https://blog.csdn.net/lufeisan/article/details/53416122>

<https://www.jianshu.com/p/a0a81062cdb4>



# pam进程

<https://www.cnblogs.com/ilinuxer/p/5087447.html>

<https://blog.csdn.net/yuanlaijike/article/details/78726606>





sssd守护进程

<http://blog.sina.com.cn/s/blog_588c88cb0100ywoh.html>



<https://yq.aliyun.com/articles/524945>



<http://www.myhack58.com/Article/48/66/2015/64247.htm>



<http://www.voidcn.com/article/p-ypdocglh-bub.html>



<https://blog.csdn.net/liu16659/article/details/80997333>





```shell
$# 是传给脚本的参数个数
$0 是脚本本身的名字
$1 是传递给该shell脚本的第一个参数
$2 是传递给该shell脚本的第二个参数
$@ 是传给脚本的所有参数的列表
$* 是以一个单字符串显示所有向脚本传递的参数，与位置变量不同，参数可超过9个
$$ 是脚本运行的当前进程ID号
$? 是显示最后命令的退出状态，0表示没有错误，其他表示有错误

$@区别：@*
相同点：都是引用所有参数
不同点：只有在双引号中体现出来。假设在脚本运行时写了三个参数（分别存储在12 3）则"*" 等价于 “12 3"（传递了一个参数）；而“@" 等价于 "1""2" "$3"（传递了三个参数）
$* 和 $@ 的区别
$* 和 $@ 都表示传递给函数或脚本的所有参数，不被双引号(" ")包含时，都以"$1" "$2" … "$n" 的形式输出所有参数。

但是当它们被双引号(" ")包含时，"$*" 会将所有的参数作为一个整体，以"$1 $2 … $n"的形式输出所有参数；"$@" 会将各个参数分开，以"$1" "$2" … "$n" 的形式输出所有参数。
```



最大线程数:

```shell
cat /proc/sys/kernel/threads-max
```

