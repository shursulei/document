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

perl脚本

```
system
$ARGV[n]：表示命令行参数数组的元素
my和our
<https://www.cnblogs.com/gdwkong/p/8669220.html>
<https://poi.apache.org/download.html>
```

```
**1、addgroup添加用户组**

sudo addgroup -gid 2010 cmd2//组名

cat /etc/group  //查看是否安装成功

cat /etc/group |grep cmd2  管道 grep

**2、adduser添加用户**

man adduser

sudo adduser cmd1//之后完善信息

finger cmd1查看命令

**3、groupdel删除用户**

cat /etc/group |grep cmd*/*    *是通配符*/

sudo groupdel cmd2

sudo del group cmd3/*给出提示信息*/

**4、grep搜索指定命令**

grep line*  file1

grep -r line*.

grep -r -v line*.反向查询（不包含）

grep -r -c  line*.

**5、groups查看用户组的所有用户**

**6、logout退出当前用户**

**7、newgrp登录其他的用户，只是改变登录群组**

**8、ls -a**

. 表示当前的本目录

..表示当前的副目录

.表示隐藏的目录

pwd打印当目录

cd ../..

cd $HOME

关闭防火墙：sudo ufw disable

9、


```

# linux查看系统配置常用命令

```
1、linux的cpu大小【单核还是双核】
cat /proc/cpuinfo |grep "model name" && cat /proc/cpuinfo |grep "physical id"
2、内存大小
cat /proc/meminfo |grep MemTotal
3、硬盘大小
fdisk -l |grep Disk
4、# 查看内核/操作系统/CPU信息的linux系统信息命令
uname -a 
5、查看操作系统版本，是数字1不是字母L
head -n 1 /etc/issue

cat/etc/redhat-release
6、查看CPU信息的linux系统信息命令
cat /proc/cpuinfo 
7、# 列出所有PCI设备
lspci -tv
8、列出所有USB设备的linux系统信息命令
lsusb -tv
9、列出加载的内核模块
lsmod
10、env # 查看环境变量资源
11、free -m # 查看内存使用量和交换区使用量
12、df -h # 查看各分区使用情况
```

# linux下定期删除系统日志文件

```
linux是一个很能自动产生文件的系统，日志、邮件、备份等。虽然现在硬盘廉价，我们可以有很多硬盘空间供这些文件浪费，让系统定时清理一些不需要的文件很有一种爽快的事情。不用你去每天惦记着是否需要清理日志，不用每天收到硬盘空间不足的报警短信，想好好休息的话，让我们把这个事情交给机器定时去执行吧。

 

1.删除文件命令：

find 对应目录 -mtime +天数 -name "文件名" -exec rm -rf {} \;

实例命令：

find /opt/soft/log/ -mtime +30 -name "*.log" -exec rm -rf {} \;

说明：

将/opt/soft/log/目录下所有30天前带".log"的文件删除。具体参数说明如下：

find：linux的查找命令，用户查找指定条件的文件；

/opt/soft/log/：想要进行清理的任意目录；

-mtime：标准语句写法；

+30：查找30天前的文件，这里用数字代表天数；

"*.log"：希望查找的数据类型，"*.jpg"表示查找扩展名为jpg的所有文件，"*"表示查找所有文件，这个可以灵活运用，举一反三；

-exec：固定写法；

rm -rf：强制删除文件，包括目录；

{} \; ：固定写法，一对大括号+空格+\+; 

 

2.计划任务：

若嫌每次手动执行语句太麻烦，可以将这小语句写到一个可执行shell脚本文件中，再设置cron调度执行，那就可以让系统自动去清理相关文件。

 

2.1创建shell：

touch /opt/soft/bin/auto-del-30-days-ago-log.sh

chmod +x auto-del-30-days-ago-log.sh

新建一个可执行文件auto-del-30-days-ago-log.sh，并分配可运行权限

 

2.2编辑shell脚本：

vi auto-del-30-days-ago-log.sh

编辑auto-del-30-days-ago-log.sh文件如下：

 

 !/bin/sh

find /opt/soft/log/ -mtime +30 -name "*.log" -exec rm -rf {} \;

 

ok，保存退出(:wq)。

 

2.3计划任务：

crontab -e

将auto-del-30-days-ago-log.sh执行脚本加入到系统计划任务，到点自动执行

输入：

10 0 * * * /opt/soft/log/auto-del-7-days-ago-log.sh

这里的设置是每天凌晨0点10分执行auto-del-7-days-ago-log.sh文件进行数据清理任务了。

完成以上三步，你就再也不每天惦记是否硬盘空间满了，该清理日志文件了，再也不会受到服务器硬盘空间不足的报警信息了，放心的去看书喝咖啡去吧！
```

