---
title: 四 Python基础之条件语句和循环
date: 2019-11-04 10:39:57
categories: Python
tags: python
---

### if条件语句

在`Python`中，最常用的判断自然就是`if语句`，可以使用`if ... elif ... else`来对条件进行判断，和其他一些语言不同的是，这里的`elif`是`else if`的缩写，但是这意味着在`Python`中，我们可以使用`else if`来代替`elif`呢？千万要注意，是不允许的！！必须使用`elif`的形式。

创建一个`demo.py`文件，添加如下内容：

```
  1 age = 19
  2 if age:
  3     print('age exists')
  4
  5 weight = 119
  6 if weight > 150:
  7     print('you are obese!')
  8 elif weight > 100:
  9     print('Great, you are handsome!')
 10 else:
 11     print('Um...you are too thin!')
```

可以尝试执行一下，输出结果：

```
➜  Desktop python3 demo.py
age exists
Great, you are handsome!
```

基本逻辑和其他语言几乎没什么不同，主要注意关键字和基本格式比如缩进、冒号就行了。

### 循环

##### `for ... in ...`循环

使用`for ... in ... `循环，我们可以遍历出`list`或`tuple`的元素，例如

```
  1 names = ['Jhone', 'Alice', 'Joe']
  2 for name in names:
  3     print('name is %s' % name)
  4
  5 ages = (1, 2, 3, 4, 5)
  6 for age in ages:
  7     print('age is {0}'.format(age))
```

执行结果：

```
name is Jhone
name is Alice
name is Joe
age is 1
age is 2
age is 3
age is 4
age is 5
```

##### range()函数

`Python`内置了一个函数叫做`range()`，可以生成一个有序的**整数序列**。

```
values = range(5)
for value in values:
	print('value is %d' % value)

ages = range(2, 6)
for age in ages:
	print('age is {0}'.format(age))
```

执行结果：

```
value is 0
value is 1
value is 2
value is 3
value is 4
age is 2
age is 3
age is 4
age is 5
```

从以上结果可以看出，当只有`1个参数`的时候，也就是`range(x)`的时候，**表示生成从`0`到`不大于x`的整数序列。**

若有`2`个参数的时候，也就是类似`range(x, y)`的时候，**表示生成从`x`开始到`不大于y`的整数序列。**

那么问题来了，以上生成的都是从`x开始`并且不大于`y`的后面的元素都比前面的元素`+1`的序列，那如果我想要每次都`+2`而不是`+1`呢？其实，`range()函数`还有`3`个参数的表示，类似`range(x, y, z)`，后面的`z`代表的就是`步长`，例如：

```
values = range(1, 5, 2) # 生成从1开始，元素每次递增为2并且不大于5的证书序列
for value in values:
	print('value is %d' % (value))
```

执行结果：

```
➜  Desktop python3 demo.py
value is 1
value is 3
```

问题：求1到100的偶数之和？

```
values = range(0, 101, 2)
sum = 0
for value in values:
	sum += value
print('sum is {0}'.format(sum))
```

执行结果：

```
sum is 2550
```

哇，感觉太方便了，有了`range()`函数，有时候真的是事半功倍呢。

##### while循环

这个没什么好说的，和其他语言几乎是一模一样，使用也非常简单。例如：

```
a = 5
while a > 0:
	print('a is equal to %d' % a)
	a-=2
```

执行结果：

```
➜  Desktop python3 demo.py
a is equal to 5
a is equal to 3
a is equal to 1
```

- `break`

`break`的作用是结束**本层循环**。

```
ret = 5
while ret:
	ret -= 1
	if ret == 2:
		break
	print(ret)
```

执行结果：

```
➜  Desktop python3 demo.py
4
3
```

- `continue`

`continue`的作用是结束**本次循环**，请注意和`break`的区别。

```
ret = 5
while ret:
	ret -= 1
	if ret == 2:
		continue
	print(ret)
```

```
➜  Desktop python3 demo.py
4
3
1
0
```

### 小结

- 条件语句用`if...elif...else`

- `range()`函数用于生成整数序列，可根据`步长`调整生成整数序列的间隔

- 循环有`for...in...`和`while`，根据情景取舍

- `break`退出本层循环，`continue`退出本次循环


