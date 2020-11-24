<https://docs.spring.io/spring-ldap/docs/2.3.2.RELEASE/reference/>

安装配置



openldap配置

代码开发

<https://www.cnblogs.com/lyh421/p/6688060.html>





openldap的模糊查询

```shell
ldapsearch -h "122.67.157.173" -p 389 -x -D "cn=admin,dc=icbcsas9,dc=com" -w "sas@123" -b "ou=groups,ou=das,dc=icbcsas9,dc=com" name="F_*"
```



等于(EQUAL TO)：  =
大于等于(Greater than)：  >=
小于等于(Less than)：  <=
通配符(wildcard)：  *
  逻辑运算符：
逻辑与(logical AND)：  & 
逻辑或(logical OR)：   | 
逻辑非(logical NOT)：   ! 
  以用户信息存储来举例，假设，用户目录树ou=user,dc=domain，结构如下：
dc=domain
      |-ou=user
            |-cn=zhangsan
            |-cn=lisi
            |-cn=wangwu
            |-cn=zhaoliu
用户信息属性如下：
cn=zhangsan
objectClass=top
objectClass=person
name=张三
sex=男
age=28
pwd=123456
email=zhangsan@163.com
desc=描述
  查询所有name为张三，sex为男的用户：
(&(name=张三)(sex=男))
  查询所有age不为28的用户：
(!(age=28))
  查询所有age为28，并且name不为张三的用户：
(&(age=28)(!(name=张三)))
  查询所有age为28，或者name为张三的用户：
(|(age=28)(name=张三))
  查询所有name的姓为张，或者desc包含描述的用户：
(|(name=张*)(desc=*描述*))
  查询所有有email为空的用户：
(email=)
  查询所有没有desc属性的用户：
(!(desc=*))
  查询所有有desc属性的用户：
(desc=*)





sample_demo

<https://www.novell.com/documentation/developer/samplecode/jldap_sample/>





## linux-pam

<http://www.linux-pam.org/documentation/>

