# Numpy 

Numpy的核心功能是高维数组，Numpy库中的ndarray(N-dimensional array object)对象支持多维数组，数组类型的对象本身具有大小固定、数组内元素数据类型相同等特性。Numpy也提供了大量数值运算函数，能够直接有效的进行向量、矩阵运算。

## ndarray


NumPy 最重要的一个特点是其 N 维数组对象 ndarray，它是一系列同类型数据的集合，以 0 下标为开始进行集合中元素的索引。  

ndarray 内部由以下内容组成：

- 一个指向数据（内存或内存映射文件中的一块数据）的指针。

- 数据类型或 dtype，描述在数组中的固定大小值的格子。

- 一个表示数组形状（shape）的元组，表示各维度大小的元组。

- 一个跨度元组（stride），其中的整数指的是为了前进到当前维度下一个元素需要"跨过"的字节数。


## dtype

数据类型对象是用来描述与数组对应的内存区域如何使用。  

```
import numpy as np
# 定义一个结构化数据类型对象
# 'S20' 表示字符串类型
# 'i1' 表示 int8
# 'f4' 表示 float32
student = np.dtype([('name','S20'), ('age', 'i1'), ('marks', 'f4')]) 


a = np.array([('abc', 21, 50),('xyz', 18, 75)], dtype = student) 

print(a) 

# 输出
[('abc', 21, 50.0), ('xyz', 18, 75.0)]
```

## 支持的数据类型

![](https://raw.githubusercontent.com/chenBingX/img/master/python/numpy数据类型.png)




