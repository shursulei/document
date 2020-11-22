# linux替换目录下所有文件中的某字符串

```shell
sed -i "s/zhangsan/lisi/g" grep zhangsan -rl /modules
```

# 解决find文件过多的情况

```shell
find . -type f -name "*.log" | xargs -i cp {} /tmp/k/
```



# 显示服务器信息

```shell
# vi /opt/scripts/system-info.sh
#!/bin/bash
echo -e "-------------------------------System Information----------------------------"
echo -e "Hostname:\t\t"`hostname`
echo -e "uptime:\t\t\t"`uptime | awk '{print $3,$4}' | sed 's/,//'`
echo -e "Manufacturer:\t\t"`cat /sys/class/dmi/id/chassis_vendor`
echo -e "Product Name:\t\t"`cat /sys/class/dmi/id/product_name`
echo -e "Version:\t\t"`cat /sys/class/dmi/id/product_version`
echo -e "Serial Number:\t\t"`cat /sys/class/dmi/id/product_serial`
echo -e "Machine Type:\t\t"`vserver=$(lscpu | grep Hypervisor | wc -l); if [ $vserver -gt 0 ]; then echo "VM"; else echo "Physical"; fi`
echo -e "Operating System:\t"`hostnamectl | grep "Operating System" | cut -d ' ' -f5-`
echo -e "Kernel:\t\t\t"`uname -r`
echo -e "Architecture:\t\t"`arch`
echo -e "Processor Name:\t\t"`awk -F':' '/^model name/ {print $2}' /proc/cpuinfo | uniq | sed -e 's/^[ \t]*//'`
echo -e "Active User:\t\t"`w | cut -d ' ' -f1 | grep -v USER | xargs -n1`
echo -e "System Main IP:\t\t"`hostname -I`
echo ""
echo -e "-------------------------------CPU/Memory Usage------------------------------"
echo -e "Memory Usage:\t"`free | awk '/Mem/{printf("%.2f%"), $3/$2*100}'`
echo -e "Swap Usage:\t"`free | awk '/Swap/{printf("%.2f%"), $3/$2*100}'`
echo -e "CPU Usage:\t"`cat /proc/stat | awk '/cpu/{printf("%.2f%\n"), ($2+$4)*100/($2+$4+$5)}' | awk '{print $0}' | head -1`
echo ""
echo -e "-------------------------------Disk Usage >80%-------------------------------"
df -Ph | sed s/%//g | awk '{ if($5 > 80) print $0;}'
echo ""
echo -e "-------------------------------For WWN Details-------------------------------"
vserver=$(lscpu | grep Hypervisor | wc -l)
if [ $vserver -gt 0 ]
then
echo "$(hostname) is a VM"
else
cat /sys/class/fc_host/host?/port_name
fi
echo ""
echo -e "-------------------------------Oracle DB Instances---------------------------"
if id oracle >/dev/null 2>&1; then
/bin/ps -ef|grep pmon
then
else
echo "oracle user does not exist on $(hostname)"
fi
echo ""
if (( $(cat /etc/*-release | grep -w "Oracle|Red Hat|CentOS|Fedora" | wc -l) > 0 ))
then
echo -e "-------------------------------Package Updates-------------------------------"
yum updateinfo summary | grep 'Security|Bugfix|Enhancement'
echo -e "-----------------------------------------------------------------------------"
else
echo -e "-------------------------------Package Updates-------------------------------"
cat /var/lib/update-notifier/updates-available
echo -e "-----------------------------------------------------------------------------"
fi
```



脚本传入参数设置



# Top的相关用法

> PID：进程的ID
> 　　USER：进程所有者
> 　　PR：进程的优先级别，越小越优先被执行
> 　　NInice：值
> 　　VIRT：进程占用的虚拟内存
> 　　RES：进程占用的物理内存
> 　　SHR：进程使用的共享内存
> 　　S：进程的状态。S表示休眠，R表示正在运行，Z表示僵死状态，N表示该进程优先值为负数
> 　　%CPU：进程占用CPU的使用率
> 　　%MEM：进程使用的物理内存和总内存的百分比
> 　　TIME+：该进程启动后占用的总的CPU时间，即占用CPU使用时间的累加值。
> 　　COMMAND：进程启动命令名称

```shell
cat /proc/进程ID/status
#VmRSS对应的值就是物理内存占用
ps aux|grep 进程名
#或者查看内存占用排名前十的：ps aux | sort -k4,4nr | head -n 10
```

# sed的相关特殊的用法

```shell
#删除找到的某一行
sed -i "/JAVA_HOME/d" $sasenvdir/sasenv_local;
#
sed -i "/-Dtk.app.lauch.config=\/sas\/sas94\/SASHome\/SASVersiondJarRepository\/picklist/a\
  -Dzookeeper.server.principal=zookeeper\/hadoop.hadoop.com\n -Djava.security.auth.login.config=/opt/hadoopclient/HDFS/hadoop/etc/hadoop/jass.conf\n" sasv9.cfg
```



# psql

```shell
#!/bin/bash
old_path=$LD_LlBRARY_PATH
export PGHOST-"/opt/huawei/Bigdata/mppdb/mppdb_tmp"
export LD_LIBRARY_PATH-"/opt/mppdb_client/psql/lib"
para_list="$*"
echo $para_list >/tmp/l.txt 
P6PASSW0RD=${para_List##*password=}
PGPASSWORD=${PGPASSWORD%% *} 
export PGPASSWORD
for line in $(echo "dbname user host port")；do 
para_list=${para_List/$line=/--$line=}
para_list=${para_List/----/--}
done
para_list=$(echo $para_list |sed -r "s/([-]*password=[^[:blank:]]*)/--no-password/")
ctlfile=`echo $para_list | awk '{print $14}'`
bak=".bak"
if [[ $ctlfile == *".ctl" ]];then cp $ctlfile $ctlfile$bak;sed -i 's/UTF-8/LATINl/g' $ctlfile;fi
/opt/mppdb_client/psql/psql_exec $para_list
export LD_LlBRARY_PATH=$old_path

```





```shell
for var in `groups $USER | awk '{for(i=3;i<=NF;i++)print $i}'`;
do hdpuser=`grep -w "$var" sasgroup_hdpuser_list.txt | awk -F ':' '{print $3}'`;
##########
done;
```

限制目录大小

```shell
kinit -kt $hadoopkeytabhome/${hdpuser} ${hdpuser/.keytab/}/hadoop;
dos2unix
```

# python调用shell

```python
import subprocess
p=subprocess.Popen("nohup echo '11'>/home/sas/1.log",shell=True, stdout=subprocess.PIPE, stderr=subprocess.STDOUT, close_fds=True)
p.returncode
print p.returncode
```

