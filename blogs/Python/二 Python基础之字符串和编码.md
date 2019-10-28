---
title: 二 Python基础之字符串和编码
date: 2019-10-27 19:34:10
categories: Python
tags: python
---

## 字符编码

- ASCII与Unicode的区别

`ASCII`编码是1个字节，而`Unicode`编码通常是2个字节。

- 计算机系统通用的字符编码方式

在计算机内存中，统一使用`Unicode`编码，当需要保存到`硬盘`或者需要传输的时候，就转换成`UTF-8`编码。


## 字符串

在`Python3`的字符串中，使用`Unicode`编码，也就是说，`Python`的字符串支持多语言。

```
>>> print('测试python字符串')
测试python字符串
```

对于单个字符的编码，`Python`提供了`ord()`函数获取**字符的整数表示**，`chr()`函数把编码转换成对应的字符：

```
>>> ord('A')
65
>>> chr(65)
'A'
>>> ord('中')
20013
>>> chr(20013)
'中'
```

由于`Python`的字符串类型是`str`，在内存中以`Unicode`表示，一个字符对应若干个字节。如果要在网络上传输或者保存到磁盘上，就需要把`str`变成以**字节为单位**的`bytes`.

`bytes`类型的数据用带`b`前缀的单引号或双引号表示：

```
name = b'ABC'
```

> 注意：`'ABC'`和`b'ABC'`虽然内容上一样，但是两者是有区别的，`'ABC'`是`str`，`b'ABC'`类型是`bytes`并且每个字符都只占用**1个字节**。

### `str`编码为`bytes`

可以将`str`类型的字符串编码为指定的`bytes`

- 通过`ASCII`编码

英文的字符串我们可以用过`ASCII`编码，但是中文是不允许的，因为超过了`ASCII`的编码范围。中文我们用`UTF-8`即可：

```
>>> 'ABC'.encode('ascii')
b'ABC'
>>> '人民币'.encode('utf-8')
b'\xe4\xba\xba\xe6\xb0\x91\xe5\xb8\x81'
```

> 注：在`bytes`中，如果无法显示成`ASCII`字符的字节，使用`\x##`表示。

### `bytes`解码为`str`

我们从网络或磁盘上读取到的都是字节流`bytes`，因此我们需要**解码**为`str`，可以使用`decode`

```
>>> b'ABC'.decode('ascii')
'ABC'
>>> b'\xe4\xba\xba\xe6\xb0\x91\xe5\xb8\x81'.decode('utf-8')
'人民币'
```

如果`bytes`中只有一小部分字节是无效的，那么可以传入`errors='ignore'`来忽略错误的字节：

```
>>> b'\xe4\xba\xba\xe6\xb0\x91\xe5\xb8\x81\xaa\xbb\xcc'.decode('utf-8', errors='ignore')
'人民币'
```

如果不使用`errors='ignore'`，则会报错

```
>>> b'\xe4\xba\xba\xe6\xb0\x91\xe5\xb8\x81\xaa\xbb\xcc'.decode('utf-8')
Traceback (most recent call last):
  File "<stdin>", line 1, in <module>
UnicodeDecodeError: 'utf-8' codec can't decode byte 0xaa in position 9: invalid start byte
```

### 计算字符数

可以使用`len()`函数来计算`str`的字符数，比如

```
>>> len('abc')
3
>>> len('我爱中国')
4
```

### 计算字节数

如果需要计算一个`str`类型的*字节数*，需要先将`str`编码成为`bytes`，然后使用`len()`函数计算

```
>>> len('abc'.encode('ascii'))
3
>>> len('我爱中国'.encode('utf-8'))
12
```

从上面可以看到，`1个中文`经过`UTF-8`编码后通常占用`3个字节`，`1个英文字符`只占用`1个字节`。

由于`Python`源代码本身也是一个`文本文件`，因此，为了保证使用的编码方式统一，防止含有中文导致乱码，以便`Python解释器`读取源文件是按照`UTF-8`的方式读取，最好在源文件头部加上：

```
#!/usr/bin/env python3
# -*- coding: utf-8 -*-
```

第一行注释是为了告诉`Linux/OS X`系统，这是一个`Python`可执行程序，`Windows`系统会忽略这个注释。

第二行注释是为了告诉`Python解释器`，按照`UTF-8`的方式读取源代码，否则如果源文件中含有**中文**的话可能会导致乱码。

> 申明了`UTF-8编码`并不意味着你的`.py文件`就是`UTF-8编码`的，必须并且要确保文本编辑器正在使用`UTF-8 without BOM编码`; 如果`.py文件`本身使用`UTF-8编码`，并且也申明了`# -*- coding: utf-8 -*-`，打开命令提示符测试就可以正常显示中文。


### 格式化

常用格式化占位符如下表：

| 占位符 | 类型 |
| --- | --- |
| %d | 整型 |
| %f | 浮点型 |
| %s | 字符串 |
| %x | 十六进制整数 |

- 通用用法

```
>>> print('I\'am %s, I am %d years old, grade percentage is %s%%' % ('langke', 18, '90'))
I'am langke, I am 18 years old, grade percentage is 90%
```

- 使用`format()`函数

```
>>> print('我是{0}, 我今年的成绩提高了{1:.2f}%'.format('刘德华', 16.394))
我是刘德华, 我今年的成绩提高了16.39%
```

## 小结

目前使用最多的编码方式是`UTF-8`，如果没有特别的业务要求，尽量使用`UTF-8`编码、解码，以避免乱码以及其他不必要的麻烦。













