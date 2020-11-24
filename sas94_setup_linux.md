#### 虚拟机安装、配置静态ip

#### linux添加共享目录

```shell

cd /mnt;mkdir cdrom
mount /dev/cdrom  /mnt/cdrom
cp VMwareTools-10.0.5-3228253.tar.gz /home/
tar -zxvf VMwareTools-10.0.5-3228253.tar.gz
./vmware-install.pl
回车
#install net-tools
#yum -y install perl perl-devel
#yum  install kernel-devel
#yum  install kernel*yum install  gcc
#vmhgfs-fuse .host:/ /mnt/hgfs#显示不了
```

#### 添加用户组和用户

```shell
#sas,sasdemo,sassrv hcb@1234
groupadd sasgroup
useradd sas -g sasgroup -d /home/sas -m -u 1401;passwd sas;
useradd sasdemo -g sasgroup -d /home/sasdemo -m -u 1402;passwd sasdemo;
useradd sassrv -g sasgroup -d /home/sassrv -m -u 1403;passwd sassrv;
useradd sasadm -g sasgroup -d /home/sasadm -m -u 1404;passwd sasadm;
wedwah  sas@2020
```

#### 修改/etc/hosts

```txt
192.168.93.41 sasserver
```

#### 关闭防火墙

```shel
service iptables stop
```



#### 开始安装【OA】

中间部分省略

```shell
mkdir -p /sas/sas94/SASHome /sas/sas94/Config;
chown sas:sasgroup /sas/sas94/SASHome /sas/sas94/Config
#mkdir -p /home/sas94/Config
#chown sas:sasgroup /home/sas94/Config
export DISPLAY=192.168.93.1:0.0
cd /mnt/hgfs/sas94/
./setup.sh
chown root:root setuid.sh
cd /sas/sas94/SASHome/SASFoundation/9.4/utilities/bin/


```

```shell
mkdir -p /etc/opt/vmware/vfabric/
chown -R sas:sasgroup /etc/opt/vmware/vfabric/
```

增大内存

修改limits

验证结果

#### 启停顺序

```
4 SAS服务启停
SAS服务启动顺序:
SAS [Config-Lev1] SASMeta - Metadata Server
SAS [Config-Lev1] Object Spawner
SAS [Config-Lev1] Cache Locator on port 41415
SAS [Config-Lev1] JMS Broker on port 61616
SAS [Config-Lev1] DIP JobRunner
SAS [Config-Lev1] httpd - WebServer 
SAS [Config-Lev1] Web Infrastructure Platform Data Server
SAS [Config-Lev1] SASServer1_1 - WebAppServer
SAS [Config-Lev1] SAS Environment Manager
SAS [Config-Lev1] SAS Environment Manager Agent
SAS Deployment Agent

SAS服务停止顺序:
SAS Deployment Agent
SAS [Config-Lev1] SAS Environment Manager Agent
SAS [Config-Lev1] SAS Environment Manager
SAS [Config-Lev1] SASServer1_1 - WebAppServer
SAS [Config-Lev1] Web Infrastructure Platform Data Server
SAS [Config-Lev1] httpd - WebServer 
SAS [Config-Lev1] DIP JobRunner
SAS [Config-Lev1] JMS Broker on port 61616
SAS [SAS [Config-Lev1] Object Spawner
Config-Lev1] Cache Locator on port 41415
SAS [Config-Lev1] SASMeta - Metadata Server

/sas/sas94/Config/Lev1/sas.servers stop
/sas/sas94/Config/Lev1/sas.servers status
```



#### SUSE

```shell
#关闭火墙
SuSEfirewall2 stop
service SuSEfirewall2_setup stop
service SuSEfirewall2_init stop
chkconfig SuSEfirewall2_init off
chkconfig SuSEfirewall2_setup off
vi /etc/profile
export LANG=zh_CN.GBK
export LC_ALL=zh_CN.GBK
export NLS_LANG="SIMPLIFIED CHINESE_CHINA.ZHS16GBK"
export ORACLE_HOME=/local/install/cfgsas1/oracle/product/11.1.0/client_1
export LD_LIBRARY_PATH=$ORACLE_HOME/lib
export PATH=$PATH:$ORACLE_HOME/bin


vi /etc/sysconfig/language
export RC_LANG =zh_CN.GBK


```

