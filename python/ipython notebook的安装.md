一、简略版的安装
	1、安装pip工具：
		sudo apt-get install python-pip
	2、安装ipython
		sudo apt-get install ipython
	3、安装ipython[notebook] 
	    sudo apt-get install ipython-notebook 
	4、启动ipython[notebook] 
		ipython notebook
	5、安装jupyter
		pip install jupyter
二、Ubuntu系统下IPython Notebook的远程访问配置
	1、安装见上面
	2、创建登录密码

```shell
sulei@sulei-virtual-machine:~$ ipython
In [1]: from IPython.lib import passwd
In [2]: passwd()
Enter password: 
Verify password: 
Out[2]: 'sha1:e4ccaec23288:2c60e197c1455dc011b690d714f84f9317f2d99e'
注：此处的密码需记住
```
3、创建IPython notebook服务器
	Terminal下：ipython profile create myserver

```shell
sulei@sulei-virtual-machine:~$ ipython profile create myserver
[ProfileCreate] Generating default config file: u'/home/sulei/.ipython/profile_myserver/ipython_config.py'
[ProfileCreate] Generating default config file: u'/home/sulei/.ipython/profile_myserver/ipython_notebook_config.py'
[ProfileCreate] Generating default config file: u'/home/sulei/.ipython/profile_myserver/ipython_nbconvert_config.py'
```
4、修改ipython_notebook_config.py配置文件

	   vim ipython_notebook_config.py
	   sulei@sulei-virtual-machine:~/.ipython/profile_myserver$ vim ipython_notebook_config.py
	   修改的文件内容为：
		   c = get_config()
	# Kernel config
	c.IPKernelApp.pylab = 'inline'
	
	# Notebook config
	c.NotebookApp.ip='*'
	c.NotebookApp.open_browser = False
	c.NotebookApp.password = u'sha1:026678de36b2:e4b83078e02c470b15789ade069359a20b0385dd'
	#此处的密码为上面的密码
	# It's a good idea to put it on a know,fixed port
	c.NotebookApp.port = 6789
5、启动IPython notebook服务器
	ipython notebook --config=/home/sulei/.ipython/profile_myserver/ipython_notebook_config.py
	或者：
	jupyter notebook --config=/home/sulei/.ipython/profile_myserver/ipython_notebook_config.py
6、登录方式:ip:6789

三、扩展之使用HTTPS访问
	1、创建自签名的证书
	openssl req -x509 -nodes -days 365 -newkey rsa:1024 -keyout mycert.pem -out mycert.pem
	2、修改配置文件
	修改ipython_notebook_config.py配置文件，其实只需要在文件中加上一句话即可，这句话指示证书的位置：

	c.NotebookApp.certfile = u'/home/qiang/mycert.pem'
	
	c = get_config()
	
	# Kernel config
	c.IPKernelApp.pylab = 'inline'
	
	# Notebook config
	c.NotebookApp.ip='*'
	c.NotebookApp.certfile = r'/home/qiang/mycert.pem'
	c.NotebookApp.open_browser = False
	c.NotebookApp.password = u'sha1:026678de36b2:e4b83078e02c470b15789ade069359a20b0385dd'
	
	# It's a good idea to put it on a know,fixed port
	c.NotebookApp.port = 6789