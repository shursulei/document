# 前期准备

添加ansible并追加working.inventory.ini文件

vi  /datafs/viya/working/working.inventory.ini

[zk-all]

pzhisfmlpsvr03z ansible_host=122.67.141.121

pzhisfmlpsvr040 ansible_host=122.67.141.122

pzhisfmlpsvr05a ansible_host=122.67.141.162

pzhisfmlpsvr05b ansible_host=122.67.141.163

pzhisfmlpsvr05c ansible_host=122.67.141.164

# Hadoop机器集群规划



| 节点\|机器名 | 122.67.141.121 | xx.122 | 122.67.141.162 | 122.67.141.163 | 122.67.141.164 |
| ------------ | -------------- | ------ | -------------- | -------------- | -------------- |
| namenode1    | √              |        |                |                |                |
| namenode2    |                | √      |                |                |                |
| datanode1    |                |        | √              |                |                |
| datanode1    |                |        |                | √              |                |
| datanode1    |                |        |                |                | √              |
| zookeeper    | √              | √      | √              | √              | √              |
| journalnode  | √              | √      | √              | √              | √              |

# 一 .hadoop集群搭建

## 1.增加hadoop和hdfs用户并配置

(sas用户,操作都是在cas01上执行)

### 1.1 创建hadoop和hdfs用户

```shell
cd /datafs/viya/working/
ansible cas-all -b -m shell -a "useradd hadoop -g sas -d /home/hadoop -m -u 1401"
ansible cas-all -b -m shell -a "useradd hdfs-g sas -d /home/hadoop -m -u 1402"
```



### 1.2 给hadoop和hdfs赋予密码

```shell
cd /datafs/viya/working/
for hst in $(cat /datafs/viya/working/cashosts); do ssh $hst "echo 'viya@123' |passwd --stdin hadoop";done
for hst in $(cat /datafs/viya/working/cashosts); do ssh $hst "echo 'viya@123' |passwd --stdin hdfs";done
```



### 1.3 检查hadoop和hdfs用户

```shell
cd /datafs/viya/working/
ansible cas-all -b -m shell -a "hostname;id hadoop"
ansible cas-all -b -m shell -a "hostname;id hdfs"
```



### 1.4 创建hadoop目录，上传sashadoop文件

```shell
cd /datafs/viya/working/
ansible cas-all -b -m shell -a "mv /opt/hadoop /opt/hadoop_bak;mkdir -p /opt/hadoop;chown hadoop:sas /opt/hadoop"
ansible cas-all -b -m shell -a "ls -al /opt/*;"
##上传sashadoop.tar.gz文件到/fss/dns，并发送到/opt/hadoop中
sudo cp /fss/dns/sashadoop.tar.gz /opt/hadoop
sudo chown hadoop:sas /opt/hadoop/sashadoop.tar.gz
```



## 2.配置ssh免密钥登录

为hadoop和hdfs配置ssh免密钥登录[该操作在cas01上]

### 2.1hadoop配置ssh

```shell
ssh-keygen -q -t rsa -N ''-f ~/.ssh/id_rsa;
cat ~/.ssh/id_rsa.pub >> ~/.ssh/authorized_keys;
chmod 600 ~/.ssh/authorized_keys;
##分发到其他机器上
echo "phzisfmlpsvr03z
phzisfmlpsvr040
phzisfmlpsvr05a
phzisfmlpsvr05b
phzisfmlpsvr05c
phzisfmlpsvr03z.site
phzisfmlpsvr040.site
phzisfmlpsvr05a.site
phzisfmlpsvr05b.site
phzisfmlpsvr05c.site">/datafs/viya/working/cassshhosts
for hst in $(cat /datafs/viya/working/cassshhosts);do scp -pr ~/.ssh $hst:~;done;
echo "===验证hadoop是否创建成功===="
for hst in $(cat /datafs/viya/working/cassshhosts);do ssh $hst "hostname;id hadoop";done;
```



### 2.2 hdfs配置ssh

```shell
ssh-keygen -q -t rsa -N ''-f ~/.ssh/id_rsa
cat ~/.ssh/id_rsa.pub >> ~/.ssh/authorized_keys;
chmod 600 ~/.ssh/authorized_keys;
for hst in $(cat /datafs/viya/working/cassshhosts);do scp -pr ~/.ssh $hst:~;done;
echo "===验证hadoop是否创建成功===="
for hst in $(cat /datafs/viya/working/cassshhosts);do ssh $hst "hostname;id hadoop";done;
```

### 2.3 配置hadoop搭建所需要的hadoophosts(使用hadoop用户)

```shell
cat>/opt/hadoop/hadoophosts<<EOF
phzisfmlpsvr03z.site
phzisfmlpsvr05a.site
phzisfmlpsvr05b.site
phzisfmlpsvr05c.site
EOF
##第一个为namenode的域名，后面的都是datanode的域名，此处不包括namenode2
```

## 3.安装配置sashadoop

使用hadoop用户,在cas01上执行



### 3.1安装hadoop

[^安装日志]: 



```shell
cd /opt/hadoop;
tar zxvf sashadoop.tar.gz;
cd /opt/hadoop/sashadoop;
chmod -R +x *;
./hadoopInstall
#安装有日志
```

### 3.2 修改环境变量

```shell
sudo vi /etc/profile
export HADOOP_HOME=/opt/hadoop/hadoop-2.4.0
export LD_LIBRARY_PATH=\$HADOOP_HOME/bin:\$LD_LIBRARY_PATH
export PATH=\$HADOOP_HOME/bin:\$HADOOP_HOME/sbin:\$PATH
##生效
source /etc/profile
```

### 3.3 修改配置hdfs-site.xml和core-site.xml文件(使用hadoop用户)





### 3.4 分发到其他的cas节点(hadoop用户)



### 3.5 配置hadoop的datanode的数据目录

```shell
ansible cas-all -m shell -a "mkdir -p /app/hadoop/data;chown -R hadoop:sas /app/hadoop/data;chmod -R 755 /app/hadoop/data"
ansible cas-all -m shell -a "mkdir -p /app/hadoop/data;chown -R hadoop:sas /app/hadoop/data01;chmod -R 755 /app/hadoop/data01"
ansible cas-all -m shell -a "mkdir -p /app/hadoop/data;chown -R hadoop:sas /app/hadoop/data02;chmod -R 755 /app/hadoop/data02"
ansible cas-all -m shell -a "mkdir -p /app/hadoop/data;chown -R hadoop:sas /app/hadoop/data03;chmod -R 755 /app/hadoop/data03"
ansible cas-all -m shell -a "mkdir -p /app/hadoop/data;chown -R hadoop:sas /app/hadoop/data04;chmod -R 755 /app/hadoop/data04"
ansible cas-all -m shell -a "mkdir -p /app/hadoop/data;chown -R hadoop:sas /app/hadoop/data05;chmod -R 755 /app/hadoop/data05"
ansible cas-all -m shell -a "mkdir -p /app/hadoop/data;chown -R hadoop:sas /app/hadoop/data06;chmod -R 755 /app/hadoop/data06"
ansible cas-all -m shell -a "mkdir -p /app/hadoop/data;chown -R hadoop:sas /app/hadoop/data07;chmod -R 755 /app/hadoop/data07"
ansible cas-all -m shell -a "mkdir -p /app/hadoop/data;chown -R hadoop:sas /app/hadoop/data08;chmod -R 755 /app/hadoop/data08"
ansible cas-all -m shell -a "mkdir -p /app/hadoop/data;chown -R hadoop:sas /app/hadoop/data09;chmod -R 755 /app/hadoop/data09"
ansible cas-all -m shell -a "mkdir -p /app/hadoop/data;chown -R hadoop:sas /app/hadoop/data10;chmod -R 755 /app/hadoop/data10"
#
ansible cas-all -m shell -a "chown -R hadoop:sas /opt/hadoop/*"
ansible cas-all -m shell -a "chown -R hadoop:sas /app/hadoop/*"
```

## 4.搭建zookeeper集群

hadoop用户

### 4.1 修改server配置文件

```shell
tar zxvf zookpeer-3.4.12.tar.gz;
cat>/opt/hadoop/zookeer-3.4.12/zkServer<<EOF
linux-109129
linux-109130
linux-109131
EOF
```



### 4.2  修改zoo.cfg配置文件

```shell
cp $ZOOKEEPER_HOME/conf/zoo_sample.cfg $ZOOKEEPER_HOME/conf/zoo.cfg
cd $ZOOKEEPER_HOME/conf/
sed '12s/\/tmp\/zookpeer/\/app\/hadoop\/zookeeper/g' zoo.cfg
sed '$a\server.1=linux-109129:2888:3888' zoo.cfg
sed '$a\server.2=linux-109130:2888:3888' zoo.cfg
sed '$a\server.3=linux-109131:2888:3888' zoo.cfg
```



### 4.3 修改zookeeper的日志输出文件

```shell
vi /opt/hadoop/zookeer-3.4.12/bin/zkEnv.sh
ZOO_LOG_DIR="/app/hadoop/zookeeper/logs"
ZOO_LOG4J_PROP="INFO,ROLLINGFILE"
```



### 4.4 将zookeeper的分发到其他节点上



### 4.5 编辑myid文件

### 4.6 启动zookeeper

## 5.启动HA

### 5.1启动journalnodes

### 5.2格式化namenode(使用hadoop用户操作)

### 5.3格式化zkfc(hadoop用户)

### 5.4启动hadoop的其他几点

### 5.5 验证

# 二.hadoop deployment configuring cas sashdata access to HDFS

2.1修改casconfig_usermods.lua文件(sas用户)

```shell
#HADOOP_NAMENODE对应的时namenode1和namenode2
#分发到其他cas节点
vi /opt/sas/viya/config/etc/cas/default/casconfig_usermods.lua

cas.colocation='hdfs'

env.HADOOP_NAMENODE='pzhisfmlpsvr03z :pzhisfmlpsvr040'

env.HADOOP_HOME='/opt/hadoop/hadoop-2.4.0'
```

2.2部署SAS Plugin-ins for hadoop

1.检查所有cas节点是否有SAS Plugin-ins for hadoop的安装包

```shell
ansible cas-all -m shell -a "ls -ld /opt/sas/viya/home/SASFoundation/hdatplugins/*"
#返回文件
sashdat-install.sh
sashdat-03.04.gz
```

1.2 sas用户拷贝到/tmp用户

```shell
sudo cp /opt/sas/viya/home/SASFoundation/hdatplugins/sashdat-install.sh /tmp
sudo cp /opt/sas/viya/home/SASFoundation/hdatplugins/sashdat-03.04.gz /tmp
chmod 755 /tmp/sashdat-install.sh /tmp/sashdat-03.04.gz
```

1.3 安装sashdat-install.sh

使用sas用户登录cas01服务器，运行如下的命令

```shell
cd /datafs/viya/working
ansible cas-all -m shell -a "hostname;sudo echo 'hadoop ALL=(ALL) NOPASSWD:ALL'|sudo tee /etc/sudoers.d/00-hadoop;"
ansible cas-all -m shell -a "hostname;sudo echo 'hdfs ALL=(ALL) NOPASSWD:ALL'|sudo tee /etc/sudoers.d/00-hdfs;"
sudo su - hadoop
cd /tmp
./sashdat-install.sh -x -check -hostfile /datafs/viya/working/cashosts
#重命名hadoop中的sas jar目录
cd /datafs/viya/working
ansible cas-all -m shell -a "mv /opt/hadoop/hadoop-2.4.0/share/hadoop/sas /opt/hadoop/hadoop-2.4.0/share/hadoop/sas_bak"
##使用hadoop用户
su - hadoop
cd /tmp
./sashdat-install.sh -add -hostfile /datafs/viya/working/cashosts -hdathome /opt/hadoop
```

1.4编辑hdfs-site.xml文件

```shell
sed -i -- "s,lasr,cas,g" /opt/hadoop/hadoop-2.4.0/etc/hadoop/hdfs-site.xml
sed -i '$d'/opt/hadoop/hadoop-2.4.0/etc/hadoop/hdfs-site.xml
echo "<property>
<name>com.sas.cas.hadoop.short.circuit.command</name>
<value>/opt/hadoop/HDATHome/bin/sascasfd<value>
</property>
<property>
<name>dfs.namenode.acls.enabled</name>
<value>/opt/hadoop/HDATHome/bin/sascasfd<value>
</property>
<property>
<name>dfs.namenode.acls.enabled</name>
<value>true<value>
</property>" >>/opt/hadoop/hadoop-2.4.0/etc/hadoop/hdfs-site.xml
##分发
```

2.修改hadoop-env.sh，加到最后

```shell
vi /opt/hadoop/hadoop-2.4.0/etc/hadoop/hadoop-env.sh
export HADOOP_CLASSPATH=/opt/hadoop/HDATHome/lib/*:$HADOOP_CLASSPATH
##分发到其他的cas上
```

3.修改cas_usermods.settings

```shell
vi /opt/sas/viya/config/etc/cas/default/cas_usermods.settings

export HADOOP_HOME=/opt/hadoop/hadoop-2.4.0
export LD_LIBRARY_PATH=$HADOOP_HOME/bin:$HADOOP_HOME/sbin:$LD_LIBRARY_PATH
export PATH=$HADOOP_HOME/bin:$HADOOP_HOME/sbin:$PATH

##分发到其他的cas节点上
```

4.验证

https://mlp-web-viyasec.sdc.cs.icbc/SASStudionV

code

```scala
cas mysession;
caslib testhdata datasource=(srctype="hdfs") path="/hdata/data/data000"
caslib _all_assign;
proc casutil;
load data=sashelp.zipcode;
run;
proc casutil;
save casdata="zipcode" replace;
run;
```



# 三、ACL和目录权限大小的配置