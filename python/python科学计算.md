一、NumPy-快速处理数据
1、astype对数组元素类型进行转换
t1=np.array([1,2,3,4],dtype=np.float)
t2=np.array([1,2,3,4],dtype=np.complex)
t3=t1.astype(np.int32)
t4=t2.astype(np.complex64)
2、自动生成数组arange
 np.arange(0,1,0.1)
array([ 0. ,  0.1,  0.2,  0.3,  0.4,  0.5,  0.6,  0.7,  0.8,  0.9])
linspace()指定开始值、终值和元素个数来创建表示等差数列的一维数组，endpoint参数指定是否包含终值，默认值为True
np.linspace(0,1,10)#步长为1/9
array([ 0.        ,  0.11111111,  0.22222222,  0.33333333,  0.44444444,
        0.55555556,  0.66666667,  0.77777778,  0.88888889,  1.        ])
np.linspace(0,1,10,endpoint=False)#步长为1/10
array([ 0. ,  0.1,  0.2,  0.3,  0.4,  0.5,  0.6,  0.7,  0.8,  0.9])
logspace()创建等比数组，基数通过base的值指定，默认值为10
np.logspace(0, 2, 5)
array([   1.        ,    3.16227766,   10.        ,   31.6227766 ,  100.        ])
计算一个八度中所有半音的频率
np.logspace(0, 1, 12, base=2, endpoint=False)
array([ 1.        ,  1.05946309,  1.12246205,  1.18920712,  1.25992105,
        1.33483985,  1.41421356,  1.49830708,  1.58740105,  1.68179283,
        1.78179744,  1.88774863])
np.zeros()将数组元素初始化为0，ones初始化为1
np.zeros(4, np.int)
array([0, 0, 0, 0])
empty()创建数组所使用的内存
np.empty((2,3), np.int)
array([[ 101328520,  101371720,  101371760],
       [ 840969264,  691347500, 1699938314]])
full()将数组元素初始化为指定值
np.full(4, np.pi)
array([ 3.14159265,  3.14159265,  3.14159265,  3.14159265])
zeros_like()和zeros(a.shape,a.dtpye)的效果相同
frombuffer()、fromstring()、fromfile()等函数是从字节序列或文件创建数组
>>> s = "abcdefgh"
>>> np.fromstring(s, dtype=np.int8)
>>> array([ 97,  98,  99, 100, 101, 102, 103, 104], dtype=int8)
>>> fromfunction()函数创建数组：第一个参数是计算每个数组元素的函数，第二个函数是指定数组的形状
>>> 九九乘法表
>>> def func2(i, j):
>>>     return (i + 1) * (j + 1)
>>> np.fromfunction(func2, (9,9))
>>> array([[  1.,   2.,   3.,   4.,   5.,   6.,   7.,   8.,   9.],
>>>        [  2.,   4.,   6.,   8.,  10.,  12.,  14.,  16.,  18.],
>>>        [  3.,   6.,   9.,  12.,  15.,  18.,  21.,  24.,  27.],
>>>        [  4.,   8.,  12.,  16.,  20.,  24.,  28.,  32.,  36.],
>>>        [  5.,  10.,  15.,  20.,  25.,  30.,  35.,  40.,  45.],
>>>        [  6.,  12.,  18.,  24.,  30.,  36.,  42.,  48.,  54.],
>>>        [  7.,  14.,  21.,  28.,  35.,  42.,  49.,  56.,  63.],
>>>        [  8.,  16.,  24.,  32.,  40.,  48.,  56.,  64.,  72.],
>>>        [  9.,  18.,  27.,  36.,  45.,  54.,  63.,  72.,  81.]])
>>> 3.存储元素
>>> 二、SciPy-数值计算库
>>> 2.1常数和特殊函数
>>> scipy的constants模块包含众多物理常数
>>> from scipy import constants as C
>>> print C.c#真空中的光速
>>> print C.h#普朗克常数
>>> 299792458.0
>>> 6.62606957e-34
>>> C.physical_constants["electron mass"]
>>> (9.10938291e-31, 'kg', 4e-38）
>>> 在字典physical_constants中，分别表示常数值，单位，误差

ellipj()支持广播运算
import numpy as np
import scipy.special as S
m = np.linspace(0.1, 0.9, 4)
u = np.linspace(-10, 10, 200)
results = S.ellipj(u[:, None], m[None, :])
print [y.shape for y in results]
结果：[(200, 4), (200, 4), (200, 4), (200, 4)]
2.2optimize拟合与优化
fsolve()函数：fsolve(func,x0)func计算方程组的误差，x0为未知数的初始值


八、OpenCV-图像处理和计算机视觉