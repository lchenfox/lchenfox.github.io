
---
title: 五 Python基础之dict和set
date: 2019-11-08 17:03:14
categories: Python
tags: python
---

## 前言

`Python`中内置了`dict`和`set`，`dict`也就是我们常说的`字典`。`dict`的最大好处就是取值非常快，给定一个`key`，我们可以快速获取到它对应的`value`。也正是如此，`dict`也被广泛应用在我们的实际开发当中。

`set`，顾名思义，也就是`集合`，和我们初中学习过的数学知识一样，集合具有`确定性`、`无序性`、`互异性`，回顾一下：

- 确定性

给定一个集合，任给一个元素，该元素或者属于或者不属于该集合，二者必居其一，不允许有模棱两可的情况出现

- 无序性

元素之间没有顺序，都是无序的。

- 互异性

给定一个集合，元素不允许重复，集合中的元素都是不相同的。

## dict

#### 初始化

`dict`可以使用字面量初始化，例如

```
dic = {'key1': 'value1', 'key2': 666}
```

有趣的是，`python`的字典可以是基本数据类型或一些不可变类型：

```
>>> dic = {'key1': 'value1', 1: 'one', False: 'False', _: '_'}
>>> dic['key1']
'value1'
>>> dic[1] // 可以使用基本数据类型作为key
'one'
>>> dic[False]  // 可以使用bool值作为key
'False'
>>> dic[_] // 可以使用下划线_作为key
'_'
>>> dic[0] // 当key为bool值时，可以用0代表False取值，可以用1代表True取值
'False'
>>> dic[True] // 当key为1或者0时，可以用True代表1取值，可以用False代表0取值
'one'
```

但是，要注意的是，当`key`为`0`时，如果使用`False`作为`key`添加到字典，则字典的`key`还是以`0`为准，但是值会覆盖之前的值，当`key`为`1`时，也是同一个道理，例如：

```
>>> dic = {1: 'a', 0: 'b'}
>>> dic
{1: 'a', 0: 'b'}
>>> dic[True] = 'True'
>>> dic
{1: 'True', 0: 'b'}
>>> dic[False] = 'False'
>>> dic
{1: 'True', 0: 'False'}
```

同样地，当`key`为`True`或`False`时，添加`1`或`0`作为`key`添加到字典，仍然是以`True`或`False`为`key`，只是值会被覆盖，例如：

```
>>> dic = {True: 'a', False: 'b'}
>>> dic[1] = 666
>>> dic
{True: 666, False: 'b'}
>>> dic[0] = 555
>>> dic
{True: 666, False: 555}
```

也就是说，当你的`key`为`1`时，这时往字典添加`True`作为`key`时，`Python`内部会将你的`True`转化为`1`，然后判断原来的`dic`里面已经有`1`，因此`key`保持不变，值被覆盖。反之，如果用`bool`值作为`key`，再往里面添加`1`或`0`作为`key`的时候，`Python`内部会转化成`bool`值的`key`。

#### 添加值

直接通过赋值的方式往字典中添加键值对，比如：

```
>>> dic = {'key1': 'value1', 'key2': 'value2'}
>>> dic['key3'] = 'value3'
>>> dic
{'key1': 'value1', 'key2': 'value2', 'key3': 'value3'}
```

#### 判断key是否存在

- `key in dict`

使用`key in dict`的形式来判断`key`是否存在于字典`dict`中，例如：

```
dic = {'key1': 'value1', 'key2': 'value2'}
if 'key2' in dic:
	print('dic中存在key2')
else:
	print('dic中不存在key2')
```

输出：

```
dic中存在key2
```

- `dic.get(key)`

第二种方法就是通过`dic.get(key)`的形式判断，如果`key`存在，将返回`key`对应的`value`，如果`key`不存在，则返回`None`，也可以指定当`key`不存在时，返回特定的值。例如：

```
dic = {'key1': 'value1', 'key2': 'value2'}
ret = dic.get('key2')
print('ret = %s' % ret)

ret1 = dic.get('key3')
print('ret1 = %s' % ret1)

ret2 = dic.get('key4', -9999)
print('ret2 = {0}'.format(ret2))
```

输出：

```
➜  Desktop python3 demo.py
ret = value2
ret1 = None
ret2 = -9999
```

#### 删除元素

字典的删除元素方法和数组是类似的，直接使用`pop(key)`的形式，`pop(key)`删除并返回所删除的`key`对应的`value`，但是如果`key`不存在就会报错：

```
>>> dic = {'a': 1, 'b': 2}
>>> dic.pop('a')
1
>>> dic
{'b': 2}
>>> dic.pop('c')
Traceback (most recent call last):
  File "<stdin>", line 1, in <module>
KeyError: 'c'
```

#### 特点

- 查找速度快（以空间换时间）
- 占用空间大，浪费内存较多
- key必须是不可变对象

为什么要求`key`是不可变，引用[廖雪峰的官方网站](https://www.liaoxuefeng.com/wiki/1016959663602400/1017104324028448)的描述就是：

> 因为`dict`根据`key`来计算`value`的存储位置，如果每次计算相同的`key`得出的结果不同，那`dict`内部就完全混乱了。这个通过`key`计算位置的算法称为哈希算法（`Hash`）。
要保证`hash`的正确性，作为`key`的对象就不能变。在`Python`中，字符串、整数等都是不可变的，因此，可以放心地作为`key`。比如，`list`是可变的，就不能作为`key`。

## set

#### 创建集合

在`Python`中，创建集合需要一个`list`数组作为数组，这一点很有趣：

```
>>> mySet = set([1, 2, 3])
>>> mySet
{1, 2, 3}
```

#### 添加元素

使用`add(key)`的形式来添加元素到集合中

```
>>> mySet = set([1, 2, 3])
>>> mySet.add(4)
>>> mySet
{1, 2, 3, 4}
```

#### 删除元素

使用`remove(key)`的形式直接删除元素

```
>>> mySet = set([1, 2, 3])
>>> mySet.remove(2)
>>> mySet
{1, 3}
```

#### 交集

使用`&`符号求两个集合的交集

```
>>> mySet = set([1,2,3])
>>> yourSet = set([2, 3, 4])
>>> mySet & yourSet
{2, 3}
```

#### 并集

使用`|`符号求两个集合的并集

```
>>> mySet = set([1,2,3])
>>> yourSet = set([2, 3, 4])
>>> mySet | yourSet
{1, 2, 3, 4}
```

> 注意：`set`和`dict`的唯一区别是没有存储对应的`value`，因为集合具有互异性，为了保证没有重复的元素，因此集合内不可以添加**不可变对象**。


## 小结

- `dict`和`set`的`key`必须不可变，因为要保证可哈希
- 通常情况下，建议使用`str`作为`dict`的`key`

