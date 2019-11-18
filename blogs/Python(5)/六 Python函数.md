---
title: 六 Python函数
date: 2019-11-12 17:29:14
categories: Python
tags: python
---

### 调用

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

### 定义

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

##### 类型检查

数据类型检查直接使用内置函数`isinstance()函数`，例如：

```
def test1(a):
	if isinstance(a, (int, float)):
		print('you passed an int type')
	else:
		raise TypeError('What the fucking are you doing?')

def test2(b):
	if not isinstance(b, (str)):
		print('the parameter you passed is not str type')
```

```
>>> from demo import test1
>>> test1(1)
you passed an int type
>>> test1(2.2)
you passed an int type
>>> test1('1')
Traceback (most recent call last):
  File "<stdin>", line 1, in <module>
  File "/Users/langke/Desktop/demo.py", line 5, in test1
    raise TypeError('What the fucking are you doing?')
TypeError: What the fucking are you doing?

>>> from demo import test2
>>> test2(1)
the parameter you passed is not str type
```

##### 函数返回多个值

`python`的函数返回多个值的时候，事实上是返回一个元组（`tuple`），例如：

```
def func1(a, b):
	a = pow(a, 2)
	b = pow(b, 2)
	return a, b
```

执行：

```
➜  Desktop python3
Python 3.7.4 (default, Sep  7 2019, 18:27:02)
[Clang 10.0.1 (clang-1001.0.46.4)] on darwin
Type "help", "copyright", "credits" or "license" for more information.
>>> from demo import func1
>>> m, n = func1(3, 4)
>>> print(m)
9
>>> print(n)
16
>>> func1(5, 7)
(25, 49)
```

### 参数

##### 位置参数

*位置参数*就是正常的参数传递，别想复杂了，例如：

```
def fuckYou(name):
	print('name is %s' % name)
```

在上面的`fuckYou函数`中，`name`就是所谓的*位置参数*。

##### 默认参数

*默认参数*就是在函数调用的时候，给部分参数设置一个默认的初始值的参数。这个其实也很好理解，现在很多语言都提供默认参数了，比如`JavaScript`、`Swift`等等，*默认参数*在有的时候可以简化我们的代码。例如：

```
def fuck(name, gender = 'girl', age = 18):
	print('name is {0}, gender is {1}, age is {2}'.format(name, gender, age))
```

```
>>> from demo import fuck
>>> fuck('langke')
name is langke, gender is girl, age is 18
>>> fuck('Alice', 'boy')
name is Alice, gender is boy, age is 18
>>> fuck('Joe', age = 38)
name is Joe, gender is girl, age is 38
```

> 注：在`python`中，当提供多个默认参数，并且在调用的时候，如果不按顺序调用后面的某个默认参数，就需要指定需要改变的那个默认参数名，比如上面的调用`fuck('Joe', age = 38)`，需要改变默认参数`age`的值，但是默认参数`age`前面的默认参数`gender`保持不变（继续使用默认参数值），那么就需要指定`age`这个参数名，否则，直接传入参数的话，在函数内部就会一一对应。

另外，当使用默认参数的时候，也要注意，一般情况下，默认参数尽量是*不可变参数值*，如果是可变的话（比如数组，数组是一个引用类型对象），那么就会造成数据修改。例如：

```
def appendElement(arr = []):
	arr.append('python')
	return arr
```

执行结果：

```
>>> from demo import appendElement
>>> appendElement()
['python']
>>> appendElement()
['python', 'python']
>>> appendElement()
['python', 'python', 'python']
```

当然，我们也可以使用`None`空值来改造一下可变的对象参数：

```
def appendElement(arr = None):
	if arr is None:
		arr = []
	arr.append('python')
	return arr
```

执行结果：

```
>>> appendElement()
['python']
>>> appendElement()
['python']
>>> appendElement()
['python']
```

因此，为了安全起见，建议一般默认参数都是用*不可变对象*，如果非要使用*可变对象*，一定要做好安全检查。

##### 可变参数

*可变参数*就是可以传入任意个数参数，对参数不限制。在`python`中，*可变参数*可以在参数名前面加`*`号来表示这个一个*可变参数*，传入的*可变参数*在内部会转换成一个*元组（tuple）*。例如：

```
def sum(*params):
	print('params type is %s' % type(params))
	sum = 0
	for param in params:
		sum += param
	return sum
```

执行：

```
>>> from demo import sum
>>> sum()
params type is <class 'tuple'>
0
>>> sum(1, 2)
params type is <class 'tuple'>
3
>>> sum(1, 2, 3)
params type is <class 'tuple'>
6
```

那如果我们要传入一个*数组*或*元组*将其中的元素作为*可变参数*传入怎么办呢？同样地，我们还是可以在要传入的*数组*或*元组*前面加`*`号，表示将*数组*或*元组*中的元素作为*可变参数*。例如：

```
>>> sum(*[1, 2, 3])
params type is <class 'tuple'>
6
>>> sum(*(4, 5, 6))
params type is <class 'tuple'>
15
```

哇，就是这么吊！

##### 关键字参数

*关键字参数*在参数名前面加`**`表示可传入一个*关键字参数*，传入*关键字参数后*，内部会组装成一个*字典（dict）*。例如：

```
def obtainInfo(name, age, **params):
	print('params type is %s' % type(params))
	print('name: %s\nage: %d\nparams: %s' % (name, age, params))
```

执行：

```
>>> obtainInfo('langke', 18)
params type is <class 'dict'>
name: langke
age: 18
params: {}
>>> obtainInfo('langke', 18, gender = 'boy', height = 1.88)
params type is <class 'dict'>
name: langke
age: 18
params: {'gender': 'boy', 'height': 1.88}
```

类似地，我们也可以在*字典（dict）*前面加`**`作为*关键字参数*传入：

```
dicParams = {
	'gender': 'boy',
	'height': 1.88
}
>>> obtainInfo('langke', 18, **dicParams)
params type is <class 'dict'>
name: langke
age: 18
params: {'gender': 'boy', 'height': 1.88}
```

我们也可以对*关键字参数*进行判断某个`key`是否存在，例如：

```
def person(name, age, **params):
	if 'city' in params:
		print('city exists')
	print(name, age, params)
person('langke', 18, city = 'Hangzhou')
```

执行及结果：

```
➜  Desktop python3 demo.py 
city exists
langke 18 {'city': 'Hangzhou'}
```

##### 命名关键字参数

*命名关键字参数*使用`*`号来分割前后参数，`*`号后面的参数就叫做*命名关键字参数*。例如：

```
def person(name, age, *, city, height):
	print('name:{0}, age:{1}, city:{2}, height:{3}'.format(name, age, city, height))
```

其中，`city`和`height`就是*命名关键字参数*，而且所有的*命名关键字参数必须指定传入值*。例如：

```
>>> person('langke', 18, city = 'Hangzhou', height = 163)
name:langke, age:18, city:Hangzhou, height:163
```

如果少了某个*关键字参数*，就会报错。例如：

```
>>> person('langke', 18, city = 'Hangzhou')
Traceback (most recent call last):
  File "<stdin>", line 1, in <module>
TypeError: person() missing 1 required keyword-only argument: 'height'
```

关键字参数可以互换位置，但是必须要注意的是，关键字参数和关键字参数前面的位置参数不能调换。例如：

关键字参数`city`和`height`交换位置：


```
>>> person('langke', 18, height = 163, city = 'Hangzhou')
name:langke, age:18, city:Hangzhou, height:163
```

*关键字参数*和*位置参数*交换位置，比如把*关键字参数`height`*放在*位置参数`age`*位置就报错：

```
>>> person('langke', height = 163, 18, city = 'Hangzhou')
  File "<stdin>", line 1
SyntaxError: positional argument follows keyword argument
```

若函数中已经有*可变参数*，那么*命名关键字参数*就不再需要`*`号了。例如：

```
def person(name, age, *args, city, height):
	print(args)
	print('name:{0}, age:{1}, city:{2}, height:{3}'.format(name, age, city, height))
```

调用函数：

```
>>> person('langke', 18, city = 'Hangzhou', height = 163)
()
name:langke, age:18, city:Hangzhou, height:163

>>> person('langke', 18, *[1, 2, 3], city = 'Hangzhou', height = 163)
(1, 2, 3)
name:langke, age:18, city:Hangzhou, height:163

>>> person('langke', 18, 1, 2, 3, city = 'Hangzhou', height = 163)
(1, 2, 3)
name:langke, age:18, city:Hangzhou, height:163
```

最后，*命名关键字参数*可以有默认值（类似默认参数）。例如：

```
def person(name, age, *, city = 'Hangzhou', height):
	print('name:{0}, age:{1}, city:{2}, height:{3}'.format(name, age, city, height))
```

执行：

```
>>> person('langke', 18, height = 163)
name:langke, age:18, city:Hangzhou, height:163
```

##### 参数组合

*参数组合*也就是使用*位置参数*、*默认参数*、*可变参数*、*命名关键字参数*、*关键字参数*结合起来使用，但是参数定义的顺序必须是：*位置参数*、*默认参数*、*可变参数*、*命名关键字参数*、*关键字参数*。例如：

```
def f1(a, b, c=0, *args, **kw):
    print('a =', a, 'b =', b, 'c =', c, 'args =', args, 'kw =', kw)

def f2(a, b, c=0, *, d, **kw):
    print('a =', a, 'b =', b, 'c =', c, 'd =', d, 'kw =', kw)
```

调用：

```
>>> f1(1, 2)
a = 1 b = 2 c = 0 args = () kw = {}

>>> f1(1, 2, c=3)
a = 1 b = 2 c = 3 args = () kw = {}

>>> f1(1, 2, 3, 'a', 'b')
a = 1 b = 2 c = 3 args = ('a', 'b') kw = {}

>>> f1(1, 2, 3, 'a', 'b', x=99)
a = 1 b = 2 c = 3 args = ('a', 'b') kw = {'x': 99}

>>> f2(1, 2, d=99, ext=None)
a = 1 b = 2 c = 0 d = 99 kw = {'ext': None}
```

最后一点要注意的是，调用上面的函数我们也可以只使用`元组（tuple）`和`数组（list）`的方式传入参数：

```
>>> args = (1, 2, 3, 4)
>>> kw = {'d': 99, 'x': '#'}
>>> f1(*args, **kw)
a = 1 b = 2 c = 3 args = (4,) kw = {'d': 99, 'x': '#'}

>>> args = (1, 2, 3)
>>> kw = {'d': 88, 'x': '#'}
>>> f2(*args, **kw)
a = 1 b = 2 c = 3 d = 88 kw = {'x': '#'}
```

所以，对于任意函数，都可以通过类似`func(*args, **kw)`的形式调用它，无论它的参数是如何定义的。

### 递归


