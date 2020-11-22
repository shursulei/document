# 前提条件

1、SAS Viya已部署Access to Hadoop组件

2、安装Hadoop客户端，测试Hadoop的版本为华为Hadoop C70

3、需要验证hadoop的配置文件和krb5.conf等文件是否有改动，包括生产环境中是否有ip改动

# hadoopC60备份

```shell
#先检查目录是否存在，生产环境中应该已经存在
cd /datafs/viya/working/
ansible all -m shell -a "ls -l /opt/hadoopclient"
ansible all -m shell -a "ls -l /opt/hadoop/conf"
ansible all -m shell -a "ls -l /opt/hadoop/jars"
#将已经存在的hadoopclient客户端进行复制备份
ansible all -m shell -a "mv /opt/hadoopclient /opt/hadoopclient20190620"
#hadoopclient20190620/conf20190620/jars20190620	时间可以修改
ansible all -m shell -a "mv /opt/hadoop/conf /opt/hadoop/conf20190620"
ansible all -m shell -a "mv /opt/hadoop/jars /opt/hadoop/jars20190620"
```



# 操作步骤

## 1、所有viya节点安装Hadoop客户端

### 1.1、检查目录是否存在。

```shell
#使用root用户
ansible all -m shell -a "ls -l /opt/hadoopclient"
ansible all -m shell -a "ls -l /opt/hadoop/conf"
ansible all -m shell -a "ls -l /opt/hadoop/jars"
```

### 1.2、如果不存在需要创建，解压到所有viya节点的客户端

```shell
#上传Hadoop客户端安装包/tmp/hadooptmp，并解压，执行安装命令
ansible all -m shell -a "cd /tmp/hadooptmp;tar xvf FusionInsight_Services_Client.tar"
ansible all -m shell -a "cd /tmp/hadooptmp;tar xvf FusionInsight_Services_ClientConfig.tar"
ansible all -m shell -a "cd /tmp/hadooptmp/FusionInsight_Services_ClientConfig;./install.sh /opt/hadoopclient"

#suse12.3下面有ntpd服务的问题，将install.sh里验证ntpd服务注释掉
ansible all -m shell -a "sed -i "s/checkNTPClient ||/#checkNTPClient ||/g" install.sh"
ansible all -m shell -a "systemctl start ntpd"


```

### 1.3、准备配置文件

```shell
#copy配置文件
ansible sas-all -m copy -a  "src=/root/tmp/hadoopC70/user.keytab  dest=/opt/hadoop/ owner=sas group=sas mode=0755" -b
ansible sas-all -m copy -a  "src=/root/tmp/hadoopC70/krb5.keytab  dest=/opt/hadoop/ owner=sas group=sas mode=0755" -b
```

### 1.4、从hadoop客户端中提取jar包和配置文件

```shell
ansible all -m shell -a “/opt/hadoop;mkdir -p /opt/hadoop/jars;mkdir -p /opt/hadoop/conf;”

ansible all -m shell -a "cd /opt/hadoop;mkdir -p /opt/hadoop/conf;cd /opt;chmod -R hadoop/conf"

ansible all -m shell -a “cp /opt/hadoopclient/Hive/Beeline/lib/*.jar /opt/hadoop/jars” 

ansible all -m shell -a “cp /opt/hadoopclient/Hive/Beeline/lib/jdbc/*.jar /opt/hadoop/jars” 

ansible all -m shell -a “find /opt/hadoopclient/HDFS/hadoop/share/hadoop -name *jar|xargs -i cp {} /opt/hadoop/jars” 
ansible all -m shell -a "cp /opt/hadoopclient/Hive/config/hive-site.xml /opt/hadoop/conf"
ansible all -m shell -a "cp /opt/hadoopclient/HDFS/hadoop/etc/hadoop/hdfs-site.xml /opt/hadoop/conf"
ansible all -m shell -a "cp /opt/hadoopclient/HDFS/hadoop/etc/hadoop/core-site.xml /opt/hadoop/conf"
ansible all -m shell -a "cp /opt/hadoopclient/HDFS/hadoop/etc/hadoop/mapred-site.xml /opt/hadoop/conf"
ansible all -m shell -a "cp /opt/hadoopclient/HDFS/hadoop/etc/hadoop/yarn-site.xml /opt/hadoop/conf"
```

## 2、测试验证

