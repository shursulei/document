# Kerberos集群搭建

## 集群规划

|     机器IP      |  主机名   |         server          |     client      |
| :-------------: | :-------: | :---------------------: | :-------------: |
| 192.168.145.101 | namenode  |           N/A           |       N/A       |
| 192.168.145.102 | datanode1 | Kerberos server(master) | Kerberos client |
| 192.168.145.103 | datanode2 | Kerberos server(slave)  | Kerberos client |
| 192.168.145.105 | datanode3 | Kerberos server(slave)  | Kerberos client |

## NTP同步时间服务器

```shell
rpm -qa | grep ntp
##yum –y remove###删除已存在
yum –y install ntp
/bin/systemctl start  ntpd.service

```



## Kerberos 安装

## Kerberos-server安装

### 1 datanode1作为Kerberos主节点安装服务：

[kerberos的官网]: http://web.mit.edu/kerberos/krb5-latest/doc/mitK5defaults.html#paths



```shell
#安装
yum install krb5-libs krb5-server krb5-workstation
#查看所有安装包
[root@datanode1 ~]# rpm -qa  | grep krb5
krb5-libs-1.15.1-37.el7_6.x86_64
krb5-workstation-1.15.1-37.el7_6.x86_64
krb5-server-1.15.1-37.el7_6.x86_64
krb5-devel-1.15.1-37.el7_6.x86_64


```

### 2 datanode1配置

kdc服务器包含三个配置文件(

[参考链接]: https://www.cnblogs.com/mantoudev/p/9460712.html

)：

```shell
# 集群上所有节点都有这个文件而且内容同步
/etc/krb5.conf
# 主服务器上的kdc配置
/var/kerberos/krb5kdc/kdc.conf
# 能够不直接访问 KDC 控制台而从 Kerberos 数据库添加和删除主体，需要添加配置
/var/kerberos/krb5kdc/kadm5.acl

```

修改配置文件

```shell
#hosts配置
192.168.145.101 namenode.site namenode
192.168.145.102 datanode1.site datanode1
192.168.145.103 datanode2.site datanode2
192.168.145.104 SecondNamenode.site SecondNamenode
192.168.145.105 datanode3.site datanode3
1. 首先配置/etc/krb5.conf文件：
[root@datanode1 ~]# cat /etc/krb5.conf
# Configuration snippets may be placed in this directory as well
includedir /etc/krb5.conf.d/

[logging]
 default = FILE:/var/log/krb5libs.log
 kdc = FILE:/var/log/krb5kdc.log
 admin_server = FILE:/var/log/kadmind.log

[libdefaults]
 dns_lookup_realm = false
 ticket_lifetime = 31356000
 renew_lifetime = 7d
 forwardable = true
 rdns = false
 pkinit_anchors = /etc/pki/tls/certs/ca-bundle.crt
 default_realm = HADOOP.COM
 default_ccache_name = /tmp/krb5cc_%{uid}#KEYRING:persistent:%{uid}
 default_tgs_enctypes=rc4-hmac
 default_tkt_enctypes = rc4-hmac
 permitted_enctypes = rc4-hmac
 udp_preference_limit = 1
 kdc_timeout = 3000

[realms]
 HADOOP.COM = {
  kdc = datanode1.site
  kdc = datanode2.site
  admin_server = datanode1.site
 }

[domain_realm]
 .hadoop.com = HADOOP.COM
 hadoop.com = HADOOP.COM


#2.配置/var/kerberos/krb5kdc/kdc.conf文件
#此处为EXAMPLE.COM与/etc/krb5.conf中的配置保持一致。
[root@datanode1 ~]# cat /var/kerberos/krb5kdc/kdc.conf 
[kdcdefaults]
 kdc_ports = 88
 kdc_tcp_ports = 88

[realms]
 HADOOP.COM = {
  master_key_type = aes256-cts
  kadmin_port = 749
  acl_file = /var/kerberos/krb5kdc/kadm5.acl
  dict_file = /usr/share/dict/words
  admin_keytab = /var/kerberos/krb5kdc/kadm5.keytab
  supported_enctypes = aes256-cts:normal aes128-cts:normal
        #des3-hmac-sha1:normal arcfour-hmac:normal camellia256-cts:normal camellia128-cts:normal des-hmac-sha1:normal des-cbc-md5:normal des-cbc-crc:normal
 }


#3. 创建/var/kerberos/krb5kdc/kadm5.acl
#内容为：*/admin@EXAMPLE.COM *
#代表名称匹配/admin@EXAMPLE COM 都认为是admin，权限是 * 代表全部权限。
#在KDC上我们需要编辑acl文件来设置权限，该acl文件的默认路径是 /var/kerberos/krb5kdc/kadm5.acl（也可以在文件kdc.conf中修改）。

#Kerberos的kadmind daemon会使用该文件来管理对Kerberos database的访问权限。对于那些可能会对pincipal
#产生影响的操作，acl文件也能控制哪些principal能操作哪些其他pricipals。
[root@datanode1 ~]# cat  /var/kerberos/krb5kdc/kadm5.acl
*/hadoop@HADOOP.COM	*

```



### 3.创建Kerberos数据库

此步可能用时较长，创建完成会在`/var/kerberos/krb5kdc/`下面生成一系列文件。并且会提示输入数据库管理员的密码。

```shell
kdb5_util create -r HADOOP.COM -s
##此处创建的key是master
```

其中，`[-s]`表示生成stash file，并在其中存储master server key（krb5kdc）；还可以用[-r]来指定一个realm name —— 当krb5.conf中定义了多个realm时才是必要的。

如果需要重建数据库，将`/var/kerberos/krb5kdc`目录下的principal相关的文件删除即可.

当Kerberos database创建好后，可以看到目录 `/var/kerberos/krb5kdc` 下生成了几个文件：

```shell
/var/kerberos/krb5kdc
[root@datanode1 krb5kdc]# ls -al
total 44
drwxr-xr-x. 2 root root   169 Jun  4 21:44 .
drwxr-xr-x. 4 root root    33 Jun  4 12:47 ..
-rw-------. 1 root root    75 Jun  4 14:05 .k5.HADOOP.COM
-rw-------. 1 root root    22 Jun  4 13:28 kadm5.acl
-rw-------. 1 root root   156 Jun  4 21:44 kadm5.keytab
-rw-------. 1 root root   479 Jun  4 21:41 kdc.conf
-rw-------. 1 root root 20480 Jun  4 21:55 principal
-rw-------. 1 root root  8192 Jun  4 14:05 principal.kadm5
-rw-------. 1 root root     0 Jun  4 14:05 principal.kadm5.lock
-rw-------. 1 root root     0 Jun  4 21:55 principal.ok

[root@datanode1 krb5kdc]# pwd
/var/kerberos/krb5kdc
```

### 4. 添加database administrator



```shell
#为Kerberos database添加administrative principals (即能够管理database的principals) —— 至少要添#加1个principal来使得Kerberos的管理进程kadmind能够在网络上与程序kadmin进行通讯。
#创建管理员并输入密码`admin`。kadmin.local可以直接运行在KDC上，而无需通过Kerberos认证。
#为用户设置密码:

[root@datanode1 krb5kdc]# kadmin.local -q "addprinc admin/admin"
Authenticating as principal root/admin@HADOOP.COM with password.
WARNING: no policy specified for admin/admin@HADOOP.COM; defaulting to no policy
Enter password for principal "admin/admin@HADOOP.COM": 
Re-enter password for principal "admin/admin@HADOOP.COM": 
Principal "admin/admin@HADOOP.COM" created.
##输入特定密码
ktadd -k /var/kerberos/krb5kdc/kadm5.keytab kadmin/changepw
```



### 5.设置kerberos服务为开机启动，关闭防火墙

```
chkconfig krb5kdc on
chkconfig kadmin on
chkconfig iptables off
```

### 6.启动krb5kdc和kadmind进程

```shell
#########
/usr/sbin/kadmind
/usr/sbin/krb5kdc
#########
service krb5kdc start
service kadmin start
service krb5kdc status
#########
/bin/systemctl start krb5kdc.service
/bin/systemctl start kadmin.service

#现在KDC已经在工作了。这两个daemons将会在后台运行，可以查看它们的日志文件
#/var/log/krb5kdc.log 和 /var/log/kadmind.log
```

### 7.检查Kerberos正常运行

```shell
kinit admin/admin
```

## Kerberos-slave安装

### 1.在server上添加principal:

```shell
[root@datanode1 ~]# kadmin.local
kadmin.local:  addprinc -randkey host/datanode1.site
kadmin.local:  addprinc -randkey host/datanode2.site
kadmin.local:  addprinc -randkey host/datanode3.site

##生产krb5.keytab
kadmin.local:  ktadd -k /etc/krb5.keytab host/datanode1.site
Entry for principal host/datanode1.site with kvno 2, encryption type aes256-cts-hmac-sha1-96 added to keytab WRFILE:/etc/krb5.keytab.
Entry for principal host/datanode1.site with kvno 2, encryption type aes128-cts-hmac-sha1-96 added to keytab WRFILE:/etc/krb5.keytab.
kadmin.local:  ktadd -k /etc/krb5.keytab host/datanode2.site
Entry for principal host/datanode2.site with kvno 2, encryption type aes256-cts-hmac-sha1-96 added to keytab WRFILE:/etc/krb5.keytab.
Entry for principal host/datanode2.site with kvno 2, encryption type aes128-cts-hmac-sha1-96 added to keytab WRFILE:/etc/krb5.keytab.
kadmin.local:  ktadd -k /etc/krb5.keytab host/datanode3.site
Entry for principal host/datanode3.site with kvno 2, encryption type aes256-cts-hmac-sha1-96 added to keytab WRFILE:/etc/krb5.keytab.
Entry for principal host/datanode3.site with kvno 2, encryption type aes128-cts-hmac-sha1-96 added to keytab WRFILE:/etc/krb5.keytab.

```

### 2.copy到slave机器

将master上的 kdc.conf, .k5.HADOOP.COM, kadm5.acl, /etc/krb5.conf, /etc/krb5.keytab拷贝至slave相应文件夹

```shell
[root@datanode1 krb5kdc]# scp kdc.conf .k5.HADOOP.COM kadm5.acl 192.168.145.103:/var/kerberos/krb5kdc/
[root@datanode1 krb5kdc]# scp kdc.conf .k5.HADOOP.COM kadm5.acl 192.168.145.105:/var/kerberos/krb5kdc/
[root@datanode1 krb5kdc]# scp /etc/krb5.keytab 192.168.145.103:/etc/krb5.keytab 
[root@datanode1 krb5kdc]# scp /etc/krb5.keytab 192.168.145.105:/etc/krb5.keytab
```

### 3.配置slave节点

在slave上添加/var/kerberos/krb5kdc/kpropd.acl 

```shell
[root@datanode2 krb5kdc]# cat /var/kerberos/krb5kdc/kpropd.acl 
host/datanode1.site@HADOOP.COM
host/datanode2.site@HADOOP.COM
host/datanode3.site@HADOOP.COM

scp kpropd.acl datanode3:/var/kerberos/krb5kdc/
#copy到datanode3上
chmod 600 kpropd.acl
```

### 4.slave启动

```shell
[root@datanode2 krb5kdc]# kpropd -S
[root@datanode3 krb5kdc]# kpropd -S
kpropd -S
```

### 5.同步数据至slave db

```shell
#在master上：
[root@datanode1 krb5kdc]# kdb5_util dump /var/kerberos/krb5kdc/slave_data
[root@datanode1 krb5kdc]# scp slave_data slave_data.dump_ok datanode2:/var/kerberos/krb5kdc/
[root@datanode1 krb5kdc]# scp slave_data slave_data.dump_ok datanode3:/var/kerberos/krb5kdc/


```



## Kerberos的client安装

### 1.集群中的其他主机安装Kerberos Client

```shell
yum install krb5-workstation krb5-libs krb5-auth-dialog
##No package krb5-auth-dialog available.
##原因是centos7/Red Hat的版本中不需要携带
#参考    https://access.redhat.com/discussions/1471433
```

配置这些主机上的`/etc/krb5.conf`，这个文件的内容与KDC中的文件保持一致即可。

### 2.在cm节点安装ldap客户端

```shell
yum install openldap-clients
```

## Kerberos使用

### 1.常用命令：

```shell
kinit admin/admin@EXAMPLE.COM # 初始化证书
klist # 查看当前证书
kadmin.local -q "list_principals"   # 列出Kerberos中的所有认证用户
kadmin.local -q "addprinc user1"  # 添加认证用户，需要输入密码
kinit user1  # 使用该用户登录，获取身份认证，需要输入密码
klist  # 查看当前用户的认证信息ticket
kinit –R  # 更新ticket
kdestroy  # 销毁当前的ticket
kadmin.local -q "delprinc user1"  # 删除认证用户
```

### 2.管理员使用

#### 2.1. 登录

登录到管理员账户，如果在本机上，可以通过kadmin.local直接登录：

```shell
[root@datanode1 ~]# kadmin.local
Authenticating as principal hadoopuser/admin@HADOOP.COM with password.
kadmin.local:  ?
```

其它机器的，先使用kinit进行验证:

```shell
[root@datanode2 keytab]# kinit admin/admin
Password for admin/admin@HADOOP.COM: 
[root@datanode2 keytab]# kadmin
Authenticating as principal admin/admin@HADOOP.COM with password.
Password for admin/admin@HADOOP.COM: 
kadmin: Incorrect password while initializing kadmin interface
[root@datanode2 keytab]# kadmin
Authenticating as principal admin/admin@HADOOP.COM with password.
Password for admin/admin@HADOOP.COM: 
kadmin:  ?
Available kadmin requests:

add_principal, addprinc, ank
```



#### 2.2增删改查账户

在管理员的状态下使用`addprinc`,`delprinc`,`modprinc`,`listprincs`命令。使用`?`可以列出所有的命令

增加账号

```shell
[root@datanode1 etc]# kadmin.local 
Authenticating as principal admin/admin@HADOOP.COM with password.
kadmin.local:  add_principal hadoopuser2/hadoop
WARNING: no policy specified for hadoopuser2/hadoop@HADOOP.COM; defaulting to no policy
Enter password for principal "hadoopuser2/hadoop@HADOOP.COM": 
Re-enter password for principal "hadoopuser2/hadoop@HADOOP.COM": 
Principal "hadoopuser2/hadoop@HADOOP.COM" created.
##密码是hadoopuser2
```

#### 2.3 生成keytab:使用xst命令或者ktadd命令

```shell
#下面的命令会报错
#kadmin:xst -k /app/keytab/hadoopuser2.keytab hadoopuser2/hadoop
#采用如下的命令
[root@datanode1 ~]# kadmin.local
Authenticating as principal hadoopuser/admin@HADOOP.COM with password.
kadmin.local:  ktadd -k /app/keytab/hadoopuser2.keytab hadoopuser2/hadoop
Entry for principal hadoopuser2/hadoop with kvno 2, encryption type aes256-cts-hmac-sha1-96 added to keytab WRFILE:/app/keytab/hadoopuser2.keytab.
Entry for principal hadoopuser2/hadoop with kvno 2, encryption type aes128-cts-hmac-sha1-96 added to keytab WRFILE:/app/keytab/hadoopuser2.keytab.
Entry for principal hadoopuser2/hadoop with kvno 2, encryption type des3-cbc-sha1 added to keytab WRFILE:/app/keytab/hadoopuser2.keytab.
Entry for principal hadoopuser2/hadoop with kvno 2, encryption type arcfour-hmac added to keytab WRFILE:/app/keytab/hadoopuser2.keytab.
Entry for principal hadoopuser2/hadoop with kvno 2, encryption type camellia256-cts-cmac added to keytab WRFILE:/app/keytab/hadoopuser2.keytab.
Entry for principal hadoopuser2/hadoop with kvno 2, encryption type camellia128-cts-cmac added to keytab WRFILE:/app/keytab/hadoopuser2.keytab.
Entry for principal hadoopuser2/hadoop with kvno 2, encryption type des-hmac-sha1 added to keytab WRFILE:/app/keytab/hadoopuser2.keytab.
Entry for principal hadoopuser2/hadoop with kvno 2, encryption type des-cbc-md5 added to keytab WRFILE:/app/keytab/hadoopuser2.keytab.
kadmin.local:  

```

### 3.用户使用

#### 3.1查看当前认证用户

```shell
[root@datanode1 ~]# klist
Ticket cache: KEYRING:persistent:0:krb_ccache_bHnZYWc
Default principal: hadoopuser@HADOOP.COM

Valid starting       Expires              Service principal
06/04/2019 17:37:31  06/05/2019 17:37:31  krbtgt/HADOOP.COM@HADOOP.COM
```

#### 3.2认证用户

```shell
[root@datanode2 keytab]# kinit -kt /app/keytab/hadoopuser2.keytab hadoopuser2/hadoop
[root@datanode2 keytab]# klist
Ticket cache: KEYRING:persistent:0:krb_ccache_1Rv3FeC
Default principal: hadoopuser2/hadoop@HADOOP.COM

Valid starting       Expires              Service principal
06/04/2019 18:53:16  06/05/2019 18:53:16  krbtgt/HADOOP.COM@HADOOP.COM
```

#### 3.3删除当前的认证的缓存

```shell
[root@datanode2 keytab]# kdestroy
Other credential caches present, use -A to destroy all
[root@datanode2 keytab]# kdestroy -c hadoopuser2/hadoop
kdestroy: No credentials cache found while destroying cache
Other credential caches present, use -A to destroy all
[root@datanode2 keytab]# kdestroy -A
[root@datanode2 keytab]# kdestroy
[root@datanode2 keytab]# klist
klist: Credentials cache keyring 'persistent:0:krb_ccache_1Rv3FeC' not found
```



# 附录[参考文章]

1.install kdcs

<http://web.mit.edu/kerberos/krb5-current/doc/krb_admins/install_kdc.html>

https://www.cnblogs.com/kisf/p/7473193.html

<https://blog.csdn.net/qq_36191174/article/details/82466743>

<https://blog.csdn.net/qq_21383435/article/details/83625252>

2.hadoop/yarn/spark结合kerberos

<https://www.cnblogs.com/kisf/p/7477440.html>

<https://blog.csdn.net/wulantian/article/details/42173023>

# Kerberos应用

场景： 我们经常要在服务器上定时的执行一些脚本，而这些脚本又需要访问另外一些服务器

问题： 我们的服务器采用的是kerberos认证，那么定时执行的脚本如何获取授权并成功访问另外一台服务器

解决办法：利用keytab来授权