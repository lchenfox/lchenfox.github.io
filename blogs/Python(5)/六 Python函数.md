---
title: 六 Python函数
date: 2019-11-12 17:29:14
categories: Python
tags: python
---

### 函数调用

函数的调用非常简单，`python`提供了很多内置函数供我们使用，比如：

获取绝对值：

```
>>> abs(-123)
123
```

求多个数中的最大值：

```
>>> max(1, 2, 3)
3
```

求多个数中的最小值：

```
>>> min(0, -1, 99)
-1
```

类型转换

```
>>> int('123')
123
>>> str(-99)
'-99'
```

以上的方法和形式与`Swift`语言十分相似，`abs()函数`、`max()函数`、`min()函数`甚至和`Swift`的`API`一模一样，包括形式和用法。

再比如，`python`也提供了`hex()函数`将一个`整数`(注意：必须是整数，否则就会报错)转换成一个`十六进制表示的字符串`。比如：

```
>>> hex(22)
'0x16'
>>> hex(99)
'0x63'
```

### 函数定义

##### 使用`def`定义函数

例如，创建一个`demo.py`文件，定义一个`test(a, b)`函数，并传入参数：

```
def test(a, b):
	if a > b:
		print('a > b')
	elif a == b:
		print('a = b')
	else:
		print('a < b')
test(5, 10)
```

执行：

```
python3 demo.py
```

输出结果：

```
a < b
```

> 注意：我们也可以在当前目录下，进入`python交互式环境`，然后使用`from demo import test`的形式导出该`demo.py`里面具体函数，`demo`是文件名，注意没有`.py`后缀，`test`是函数名，然后在交互式环境下使用`test(a, b)`函数。

##### `pass`占位符

有时候，我们可以在函数或一些程序中使用`pass`来作为占位符，比如定义一个函数，但是里面没有任何内容，执行就会报错，比如：

```
def fuck():

```

执行:

```
python3 demo.py
```

报错：

```
➜  Desktop python3 demo.py
  File "demo.py", line 2

               ^
SyntaxError: unexpected EOF while parsing
```

那想留住这个函数，后面在写函数体里面的内容怎么办呢？有`2种`方式，第一，使用`pass`，第二，使用`return`，例如：

```
def fuck():
	pass
```

或者

```
def fuck():
	return
```

这样执行`python文件`，就可以执行里面的程序而不会因为一个`空函数`而报错。

