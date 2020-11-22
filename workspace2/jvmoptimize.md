# jinfo用法说明

-flags pid ：打印当前VM的参数

-flag <name> pid：打印指定JVM的参数值 

-flag [+|-]<name> pid：设置指定JVM参数的布尔值

-flag <name>=<value> pid：设置指定JVM参数的值

-sysprops  pid : 获取当前系统参数包括-D设置的参数



```
java -XX:+PrintFlagsFinal -version|grep manageable
```



```verilog

     intx CMSAbortablePrecleanWaitMillis            = 100             {manageable}
     intx CMSWaitDuration                           = 2000            {manageable}
     bool HeapDumpAfterFullGC                       = false           {manageable}
     bool HeapDumpBeforeFullGC                      = false           {manageable}
     bool HeapDumpOnOutOfMemoryError                = false           {manageable}
    ccstr HeapDumpPath                              =                 {manageable}
    uintx MaxHeapFreeRatio                          = 100             {manageable}
    uintx MinHeapFreeRatio                          = 0               {manageable}
     bool PrintClassHistogram                       = false           {manageable}
     bool PrintClassHistogramAfterFullGC            = false           {manageable}
     bool PrintClassHistogramBeforeFullGC           = false           {manageable}
     bool PrintConcurrentLocks                      = false           {manageable}
     bool PrintGC                                   = false           {manageable}
     bool PrintGCDateStamps                         = false           {manageable}
     bool PrintGCDetails                            = false           {manageable}
     bool PrintGCTimeStamps                         = false           {manageable}
java version "1.7.0_79"
OpenJDK Runtime Environment (rhel-2.5.5.4.el6-x86_64 u79-b14)
OpenJDK 64-Bit Server VM (build 24.79-b02, mixed mode)

```



```shell
#1
/sas/sas94/SASHome/SASPrivateJavaRuntimeEnvironment/9.4/jre/bin/java -classpath /sas/sas94/SASHome/SASPrivateJavaRuntimeEnvironment/9.4/lib/tools.jar sun.tools.jinfo.JInfo -flags 11375
#2
/sas/sas94/SASHome/SASPrivateJavaRuntimeEnvironment/9.4/jre/bin/java -classpath /sas/sas94/SASHome/SASPrivateJavaRuntimeEnvironment/9.4/lib/tools.jar sun.tools.jinfo.JInfo -flag InitialHeapSize 11375

#-XX:InitialHeapSize=1073741824
#3
/sas/sas94/SASHome/SASPrivateJavaRuntimeEnvironment/9.4/jre/bin/java -classpath /sas/sas94/SASHome/SASPrivateJavaRuntimeEnvironment/9.4/lib/tools.jar sun.tools.jinfo.JInfo -flag +PrintGCDetails 11375
#4
/sas/sas94/SASHome/SASPrivateJavaRuntimeEnvironment/9.4/jre/bin/java -classpath /sas/sas94/SASHome/SASPrivateJavaRuntimeEnvironment/9.4/lib/tools.jar sun.tools.jinfo.JInfo -flag +PrintGCDetails 11375
#5
/sas/sas94/SASHome/SASPrivateJavaRuntimeEnvironment/9.4/jre/bin/java -classpath /sas/sas94/SASHome/SASPrivateJavaRuntimeEnvironment/9.4/lib/tools.jar sun.tools.jinfo.JInfo -flag -PrintGCDetails 11375
#6
/sas/sas94/SASHome/SASPrivateJavaRuntimeEnvironment/9.4/jre/bin/java -classpath /sas/sas94/SASHome/SASPrivateJavaRuntimeEnvironment/9.4/lib/tools.jar sun.tools.jinfo.JInfo -flag MaxHeapFreeRatio=10 11375

Exception in thread "main" java.io.IOException: Command failed in target VM
        at sun.tools.attach.LinuxVirtualMachine.execute(LinuxVirtualMachine.java:224)
        at sun.tools.attach.HotSpotVirtualMachine.executeCommand(HotSpotVirtualMachine.java:217)
        at sun.tools.attach.HotSpotVirtualMachine.setFlag(HotSpotVirtualMachine.java:190)
        at sun.tools.jinfo.JInfo.flag(JInfo.java:123)
        at sun.tools.jinfo.JInfo.main(JInfo.java:76)

#注意：很多运行参数是不能调整的，如果出现这种异常，说明不能调整：不能动态调整

#Exception in thread "main" java.io.IOException:Command failed in target VM

#7
/sas/sas94/SASHome/SASPrivateJavaRuntimeEnvironment/9.4/jre/bin/java -classpath /sas/sas94/SASHome/SASPrivateJavaRuntimeEnvironment/9.4/lib/tools.jar sun.tools.jinfo.JInfo -sysprops 11375
```



-D参数

-Dname=value

可以通过System.getProperty("file.encoding")得到UTF-8

而System的property是指Java虚拟机的参数。

java -cp的作用

java -Dtest.dir=/opt -cp test.jar test.Main

1.打包时指定了主类，可以直接用java -jar xxx.jar。
2.打包是没有指定主类，可以用java -cp xxx.jar 主类名称（绝对路径）。
3.要引用其他的jar包，可以用java -classpath $CLASSPATH:xxxx.jar 主类名称（绝对路径）。其中 -classpath 指定需要引入的类。







# jstack(查看线程)

```shell
/sas/sas94/SASHome/SASPrivateJavaRuntimeEnvironment/9.4/jre/bin/java -classpath /sas/sas94/SASHome/SASPrivateJavaRuntimeEnvironment/9.4/lib/tools.jar sun.tools.jstack.JStack -l 11375 >jstack.txt

```



# jmap(查看内存)

使用 jmap -histo PID 生成java堆中对象的相关信息，包含数量以及占用的空间大小

```shell
/sas/sas94/SASHome/SASPrivateJavaRuntimeEnvironment/9.4/jre/bin/java -classpath /sas/sas94/SASHome/SASPrivateJavaRuntimeEnvironment/9.4/lib/tools.jar sun.tools.jmap.JMap -histo 11375 > JMap.txt

```

  使用 jmap -dump:live,file=b.map 22467 将live进程生成java堆转储快照

```shell
/sas/sas94/SASHome/SASPrivateJavaRuntimeEnvironment/9.4/jre/bin/java -classpath /sas/sas94/SASHome/SASPrivateJavaRuntimeEnvironment/9.4/lib/tools.jar sun.tools.jmap.JMap -dump:live,file=b.map 11375

```

查看当前java进程的pid

```shell
	jmap -heap PID 
```

jstat(性能分析)

主要是用来监控 heap size 和 jvm垃圾回收情况，尤其是gc情况的监控，如果老年代发生full gc，那么很可能会导致内存泄漏的可能性

```shell
/sas/sas94/SASHome/SASPrivateJavaRuntimeEnvironment/9.4/jre/bin/java -classpath /sas/sas94/SASHome/SASPrivateJavaRuntimeEnvironment/9.4/lib/tools.jar sun.tools.jstat.Jstat -gcutil 11375

  S0     S1     E      O      P     YGC     YGCT    FGC    FGCT     GCT
  0.00  62.89  73.18  57.21  60.01   1261   49.905    56   15.055   64.960
```



```txt
S0: Survivor 0区的空间使用率 Survivor space 0 utilization as a percentage of the space's current capacity.

S1: Survivor 1区的空间使用率 Survivor space 1 utilization as a percentage of the space's current capacity.

E: Eden区的空间使用率 Eden space utilization as a percentage of the space's current capacity.

O: 老年代的空间使用率 Old space utilization as a percentage of the space's current capacity.

M: 元数据的空间使用率 Metaspace utilization as a percentage of the space's current capacity.

CCS: 类指针压缩空间使用率 Compressed class space utilization as a percentage.

YGC: 新生代GC次数 Number of young generation GC events.

YGCT: 新生代GC总时长（从应用程序启动到采样时年轻代中gc所用时间 单位：s）
	  Young generation garbage collection time.

FGC: Full GC次数 Number of full GC events.

FGCT: Full GC总时长（从应用程序启动到采样时old代(全gc)gc所用时间 单位：s）
	  Full garbage collection time.

GCT: 总共的GC时长 （从应用程序启动到采样时gc用的总时间 单位：s）Total garbage collection time.

```

*注：full gc很具有代表性，full gc次数 和时间 指标很能显示系统性能问题，这两个指标很大，很大程度上说明了程序中有问题，垃圾一直回收不掉*





# linux查看进程信息

cat /proc/进程ID/status

ps aux|grep 进程名

或者查看内存占用排名前十的：ps aux | sort -k4,4nr | head -n 10





## top命令使用方法

top工具有两种使用方法：

　　命令行加选项。其语法格式：top [选项]

　　top工具的内置命令或交互命令的使用



### **1、命令行加选项**

（1）-d n　　指定每两次屏幕信息刷新之间的时间间隔。n 为具体的秒数。

（2）-p pid　　通过指定监控进程ID来仅仅监控某个进程的状态。pid 为具体进程ID。 如果是多个进程，只要`$ top -p pid1,pid2,pid3`

（3）-q 　　该选项将使top没有任何延迟的进行刷新。如果调用程序有超级用户权限，那么top将以尽可能高的优先级运行。 

（4）-S　　指定累计模式 

（5）-s　　使top命令在安全模式中运行。这将去除交互命令所带来的潜在危险。 

（6）-i　　使top界面不显示任何闲置或者僵死进程。只显示运行中的线程。 

（7）-c　　COMMAND 选项显示整个命令行而不只是显示命令名。

（8）-b　　top显示的时候，将每一次显示的结果都打印出来，不会将上一次的结果给冲掉

（9）-u username　　显示某个用户的进程信息，username 为具体用户名，比如 root。

（10）-H　　显示线程的信息，不显示进程信息。



### 2、交互式命令

（3）A： 根据单窗口切换成多窗口，可以看到四个不同的窗口，可以通过a或者w来切换多个窗口。 四个窗口的名字分别为：Def，Job，Mem，Usr。

（1）f ： 进入一个动态配置top的界面中，按a-z即可显示或隐藏指定的列，按 Esc 或 Enter 都可保存并退回到 top 信息。

（2）o： 进入一个动态配置top的界面中， 可以改变列的显示顺序，按小写的 a-z 可以将相应的列向右移动，而大写的 A-Z 可以将相应的列向左移动。按 Esc 或 Enter 都可保存并退回到 top 信息。

（3）R： top界面第一列数据进行倒序排列。

（4）k ： 终止一个进程。系统将提示用户输入需要终止的进程PID，以及需要发送给该进程什么样的信号。一般的终止进程可以使用15信号；如果不能正常结束那就使用信号9强制结束该进程。默认值是信号15。在安全模式中此命令被屏蔽。

（5）i： 隐藏闲置和僵死进程。这是一个开关式命令。

（6）q： 退出top程序，Ctrl + C 也可以退出。

（7）S： 切换到累计模式。

（8）s : 改变两次刷新之间的延迟时间。系统将提示用户输入新的时间，单位为s。如果有小数，就换算成ms。输入0值则系统将不断刷新，默认值是5 s。需要注意的是如果设置太小的时间，很可能会引起不断刷新，从而根本来不及看清显示的情况，而且系统负载也会大大增加。

（9）r： 重新安排一个进程的优先级别。系统提示用户输入需要改变的进程PID以及需要设置的进程优先级值。输入一个正值将使优先级降低，反之则可以使该进程拥有更高的优先权。默认值是10。

（10）l：切换显示平均负载和启动时间信息。即显示隐藏第一行。

（11）m： 切换显示内存信息。即显示隐藏内存行。

（12）t ： 切换显示进程和CPU状态信息。即显示隐藏CPU行。

（13）c： 切换显示命令名称和完整命令行。 显示完整的命令。 这个功能很有用。

（14）M ： 根据驻留内存大小RES进行从大到小排序。

（15）P： 根据CPU使用百分比大小%CPU进行从高到底排序。

（16）T： 根据时间/累计时间TIME+进行从多到少排序。

（17）W： 将当前设置写入~/.toprc文件中。这是写top配置文件的推荐方法

（18）h或者? 显示帮助画面，给出一些简短的命令总结说明，按 Esc 或 Enter 都可退回到 top 信息。

（19）1：在第三行显示多核CPU信息。



## top命令补充

（1）监控Java线程数

```
ps -eLf | grep java | wc -l
```

java可以换成 COMMAND 中的程序名，来监控指定程序的线程名。

（2）监控网络客户连接数

```
netstat -n | grep tcp | grep 3306 | wc -l
```

3306 是MySQL的端口，可以换成需要监控的其他接口。

（3）进程中运行的线程数量

```
ls /proc/PID/task | wc -l
```

PID 是你想查看的进程号。一般top界面第一列就是PID。

（4）输出进程内存的状况，可以用来分析线程堆栈

```
pmap PID
```

PID 是你想查看的进程号。一般top界面第一列就是PID。

（5）CPU占用最多的前10个进程

```
ps auxw|head -1;ps auxw|sort -rn -k3|head -10
```

（6）内存消耗最多的前10个进程

```
ps auxw|head -1;ps auxw|sort -rn -k4|head -10
```

（7）虚拟内存使用最多的前10个进程

```
ps auxw|head -1;ps auxw|sort -rn -k5|head -10
```