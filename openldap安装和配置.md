1、openldap的下载地址

http://rpm.pbone.net/index.php3?stat=3&limit=2&srodzaj=3&dl=40&search=openldap2&dist[]=46

2、一般环境安装了客户端

[root@slave2 ~]# rpm -qa|grep openldap
openldap-2.4.23-32.el6_4.1.x86_64

3、

[root@master ~]# slappasswd -s viya@123

{SSHA}IIuIfFiOHwWURknfA43ebrBktUl442aR

4、修改账号和密码



[root@master ldif]# ldapadd -Y EXTERNAL -H ldapi:/// -f /etc/openldap/ldif/chrootpw.ldif 
SASL/EXTERNAL authentication started
SASL username: gidNumber=0+uidNumber=0,cn=peercred,cn=external,cn=auth
SASL SSF: 0
modifying entry "olcDatabase={0}config,cn=config"



ldapadd -Y EXTERNAL -H ldapi:/// -f /etc/openldap/ldif/chrootpw.ldif



导入schema

[root@master schema]# ldapadd -Y EXTERNAL -H ldapi:/// -f /etc/openldap/schema/core.ldif 

[root@master schema]# ldapadd -Y EXTERNAL -H ldapi:/// -f /etc/openldap/schema/cosine.ldif 

[root@master schema]# ldapadd -Y EXTERNAL -H ldapi:/// -f /etc/openldap/schema/nis.ldif 

[root@master schema]# ldapadd -Y EXTERNAL -H ldapi:/// -f /etc/openldap/schema/inetorgperson.ldif 



配置monitor.ldif

[root@master schema]# ldapadd -Y EXTERNAL -H ldapi:/// -f /etc/openldap/ldif/monitor.ldif 



# ldap命令

查看 管理员密码字段放在哪一个配置文件中

ldapsearch -H ldapi:// -LLL -Q -Y EXTERNAL -b “cn=config” “(olcRootDN=*)” dn olcRootDN olcRootPW 

查看管理员密码

[root@master ~]# slappasswd -s my-domain123456
{SSHA}xqq0XMXJ53KKaVOCpsh+bXo5zvmBkcHm



ldap的密码:123456

{SSHA}xqq0XMXJ53KKaVOCpsh+bXo5zvmBkcHm

修改密码

[root@master ldif]# cat>newpasswd.ldif<<"EOF"

> dn: olcDatabase={2}bdb,cn=config
> changetype:modify
> replace:olcRootPW
> olcRootPW:{SSHA}xqq0XMXJ53KKaVOCpsh+bXo5zvmBkcHm
> EOF

使用ldapmodify命令导入上述文件，命令如下：

ldapmodify -H ldapi:// -Y EXTERNAL -f /root/newpasswd.ldif

[root@master ldif]# ldapmodify -H ldapi:// -Y EXTERNAL -f newpasswd.ldif 
SASL/EXTERNAL authentication started
SASL username: gidNumber=0+uidNumber=0,cn=peercred,cn=external,cn=auth
SASL SSF: 0
modifying entry "olcDatabase={2}bdb,cn=config"



# jldap

```
 Search Scope：表示搜索的深度，有四个基本参数：

• SCOPE_BASE. 这个只在你已经知道了目录结构的时候使用，你用这种搜索方式输出的时候会发现什么都看不到

• SCOPE_ONE. 只搜索处在该层目录的数据（也就是说如果存在多层结构，处在该层目录一下的目录里面的数据就搜不到）。

• SCOPE_SUB. 按层次遍历，可以搜到该目录下面的所有数据，包含目录.

• SCOPE_SUBORDINATESUBTREE. 搜索的形式跟上面一个一样，但是不包含基础的数据，具体什么含义，试试就知道了。

```

