问题原因:

os.system调用之后出现网络阻塞，延迟等待的问题。

popen和system都可以执行外部命令。
popen相当于是先创建一个管道，然后fork，关闭管道的一端，执行exec，返回一个标准的io文件指针。
system相当于是先后调用了fork， exec，waitpid来执行外部命令
popen本身是不阻塞的，要通过标准io的读取（fread等）使它阻塞； //如果用fread读文件，不会阻塞,如果用来读管道、socket连接等，则可能会阻塞。
system本身就是阻塞的。

最近写的程序，要求进程在调用的外部命令运行完毕之后，再继续 向下进行。
一开始调用的popen，然后只是用了fgetc，使其阻塞，但是总是阻塞不了。原因就是如果外部命令有很多的输出内容，那fgets在得到输出的第一个字符的时候就返回了，不在阻塞了；调用fread，如果size和nitems设置的不够大，也是一样的问题。比如外部命令要输出100个字符，结果size是sizeof(char)，nitems是10，那么当fread读到地10个字符的时候，就已经满足条件了，就返回了。
正确的方法是调用system，因为system最后会调用waitpid，来等待子进程运行完毕。

```
os.system和subprocess.Popen("nohup echo '11'>/home/sas/1.log",shell=True, stdout=subprocess.PIPE, stderr=subprocess.STDOUT, close_fds=True)
#的区别

```

```python
import subprocess
p=subprocess.Popen("nohup echo '11'>/home/sas/1.log",shell=True, stdout=subprocess.PIPE, stderr=subprocess.STDOUT, close_fds=True)
p.communicate()
p.returncode
print p.returncode
```



```python
import subprocess
import traceback

try:
    cmd = "ls -lh"
    obj = subprocess.Popen(cmd,stdout=subprocess.PIPE,stderr=subprocess.PIPE,shell=True)
    obj.wait()
    
    lines = obj.stdout.readlines()
    
    if not lines or len(lines) == 0:
        line = obj.stderr.readlines()
    
    print lines
except Exception, e:
    print traceback.format_exc()
    
    #改进,创建自己流
import subprocess
import traceback
import tempfile
try:
    cmd = "ls -lh"
    out_temp = tempfile.SpooledTemporaryFile(bufsize=10*1000)
    fileno = out_temp.fileno()
    obj = subprocess.Popen(cmd,stdout=fileno,stderr=fileno,shell=True)
    obj.wait()
    out_temp.seek(0)
    lines = out_temp.readlines()
    print lines
except Exception, e:
    print traceback.format_exc()
finally:
    if out_temp:
        out_temp.close()
```



定时卡死

```python
def timeout_command(command, timeout):
	start = datetime.datetime.now()
	process = subprocess.Popen(command, bufsize=10000, stdout=subprocess.PIPE, close_fds=True)
	while process.poll() is None:
		time.sleep(0.1)
		now = datetime.datetime.now()
		if (now - start).seconds> timeout:
		try:
			process.terminate()
		except Exception,e:
			return None
		return None
	out = process.communicate()[0]
	if process.stdin:
		process.stdin.close()
	if process.stdout:
		process.stdout.close()
	if process.stderr:
		process.stderr.close()
	try:
		process.kill()
	except OSError:
		pass
	return out
```

数据流

```python
#!/usr/bin/env python
# coding: utf-8
# yc@2013/04/28

import subprocess

def test(size):
    print 'start'
    cmd = 'dd if=/dev/urandom bs=1 count=%d 2>/dev/null' % size
    p = subprocess.Popen(args=cmd, shell=True, stdout=subprocess.PIPE, stderr=subprocess.STDOUT, close_fds=True)
    #p.communicate()
    p.wait()  # 这里超出管道限制，将会卡住子进程

    print 'end'

# 64KB
test(64 * 1024)

# 64KB + 1B
test(64 * 1024 + 1)

# output :
start
end
start   #  然后就阻塞了。


```



最近在工作中发现，java调用shell脚本时，如果是批量跑的，会出现状态阻塞的问题。同理在python中调用shell的方法，有os.system和subprocess的两种方法，在实践使用过程中，同时出现这种问题。