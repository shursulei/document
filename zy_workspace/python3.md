# xlrd

```html
https://xlrd.readthedocs.io/en/latest/
https://openpyxl.readthedocs.io/en/stable/
https://pypi.org/project/xlrd/
```

# python语法工具

### python跨平台分割符

```python
import platform

if platform.system().lower() == 'windows':
    print("windows")
elif platform.system().lower() == 'linux':
    print("linux")
#目录函数分割符
def set_delimiter(dir, file):
    return os.sep.join([dir, file])
```



random的几个随机的用法

```python
list = [0,1,2,3,4]
rs = random.sample(list, 2)
#
list.append()
random.uniform(x, y) #方法将随机生成一个实数，它在 [x,y] 范围内。

choices()方法返回一个列表，其中包含从指定序列中随机选择的元素。您可以使用weights参数或cum_weights参数权衡每个结果的可能性。该序列可以是字符串，范围（range），列表，元组或任何其他种类的序列。
choices(population, weights=None, *, cum_weights=None, k=1)：从population中进行K次有放回随机选取，每次选取一个元素（注意会出现同一个元素多次被选中的情况），weights是相对权重值，population中有几个元素就要有相对应的weights值，cum_weights是累加权重值，例如，相对权重〔10, 5, 30，5〕相当于累积权重〔10, 15, 45，50〕。在内部，在进行选择之前，相对权重被转换为累积权重，因此提供累积权重节省了工作。返回一个列表。
```



```python
str() 函数
```



日期合并

```python
 datetime.combine
```



# pandas

官方文档

```html
https://pandas.pydata.org/pandas-docs/stable/reference/api/pandas.DataFrame.cov.html
```

### pandas对接mysql数据库

```html
https://stackoverflow.com/questions/10065051/python-pandas-and-databases-like-mysql
```





# python图片文字识别

## ORC系列

```shell
 pip  install pillow
 pip  install pytesseract
 pip install pytesser3
pip install wheel
#autopy下载地址
#https://pypi.org/project/autopy/#files
#https://www.lfd.uci.edu/~gohlke/pythonlibs/#autopy
 #https://tesseract-ocr.github.io/tessdoc/Downloads
 #https://tesseract-ocr.github.io/tessdoc/4.0-with-LSTM.html#400-alpha-for-windows
 #数据
 #https://github.com/tesseract-ocr/tessdoc/blob/master/Data-Files.md
 #https://github.com/tesseract-ocr/tessdata_best
```



安装

D:\software\anaconda3\Lib\site-packages\pytesseract

安装之后加入环境变量

修改配置文件

```
tesseract_cmd = 'D:/software/tesseract_ocr/setup/Tesseract-OCR/tesseract.exe'
```









## opencv

安装pip install opencv-python

地址:https://opencv-python-tutroals.readthedocs.io/en/latest/py_tutorials/py_tutorials.html

http://woshicver.com/

```shell
pip install opencv-python -i http://mirrors.aliyun.com/pypi/simple/   --trusted-host mirrors.aliyun.com
pip install opencv-contrib-python -i http://mirrors.aliyun.com/pypi/simple/   --trusted-host mirrors.aliyun.com
```



# pycharm的快捷键

```
快速复制 ctrl+D
导入  alt+enter
```





# 数据分析

## python按比例随机切分数据

```
import random

def split(full_list,shuffle=False,ratio=0.2):
    n_total = len(full_list)
    offset = int(n_total * ratio)
    if n_total==0 or offset<1:
        return [],full_list
    if shuffle:
        random.shuffle(full_list)
    sublist_1 = full_list[:offset]
    sublist_2 = full_list[offset:]
    return sublist_1,sublist_2


if __name__ == "__main__":
    li = range(5)
    sublist_1,sublist_2 = split(li,shuffle=True,ratio=0.2)

    print sublist_1,len(sublist_1)
    print sublist_2,len(sublist_2)
```





# 机器学习和数据挖掘



# tabulate

# struct模块



# PrettyTable模块