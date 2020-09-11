---
title: python&numpy的草稿本
mathjax: true
date: 2020-09-11 09:47:18
tags:
- python
- numpy
categories: 基础知识
---

关于python和numpy一些零碎的知识点

<!--more-->


## python

### 列表的深复制和浅复制

现在有
```py

>>>A = [1,2,3,[4,5]]
>>>B = A
>>>B
[1,2,3,[4,5]]
>>>A.append(6)
>>>B
[1,2,3,[4,5],6]
>>>C = A.copy()
>>>import copy
>>>D = copy.deepcopy(A)
>>>A.append(7)
>>>A[3].append(6)
>>>A
[1,2,3,[4,5,6],6,7]
>>>C
[1,2,3,[4,5,6],6]
>>>D
[1,2,3,[4,5],6]


```
- B只是A的一个别称，也就是对象的引用
- C是A的浅复制，A和C对象本身是独立的，但是它们其中包含的子对象指向的还是同一个
- D是A的深复制，完全分离，对于A的修改完全不影响D，深复制在这里使用了copy模块中的deepcopy()函数

### python2.7的继承

有关类的继承的写法，与python3有所区别，在子类init中super需要写两个参数，子类名称和self，python3不需要写这两个参数，直接super()即可

```py
class Father(object):
    def __init___(self,name):
        ...

class Child(Father):
    def __init__(self,name):
        super(Child,self).__init__(name)
        ...

```

## numpy

### argsort使用关键字的排序

```python

numpy.argsort(a, axis=-1, kind='quicksort', order=None)

```


argsort返回的是一个元素排列顺序的array，排列顺序为从大到小，可以选择的参数包括排序依据的轴，排序用的算法有mergesort, heapsort, quicksort

*最后一个参数没有完全理解，doc中原文如下，大概是说自定义域的类型排序顺序*

```c

order : str or list of str, optional

When a is an array with fields defined, this argument specifies which fields to compare first, second, etc. A single field can be specified as a string, and not all fields need be specified, but unspecified fields will still be used, in the order in which they come up in the dtype, to break ties.

```

**基本用法**

```python
one-dimensional
>>> x = np.array([3, 1, 2])
>>> np.argsort(x)
array([1, 2, 0])

two-dimensional
>>> x = np.array([[0, 3], [2, 2]])
>>> x
array([[0, 3],
       [2, 2]])
>>> np.argsort(x, axis=0)
array([[0, 1],
       [1, 0]])


```

**使用关键字排序的用法**

在定义array时添加dtype类型，并在排序中设置order的顺序

```python

>>> x = np.array([(1, 0), (0, 1)], dtype=[('x', '<i4'), ('y', '<i4')])
>>> x
array([(1, 0), (0, 1)],
      dtype=[('x', '<i4'), ('y', '<i4')])

>>> np.argsort(x, order=('x','y'))
array([1, 0])

>>> np.argsort(x, order=('y','x'))
array([0, 1])

```