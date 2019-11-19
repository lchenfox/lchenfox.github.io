---
title: (1) Swift必备Tips(第四版)
date: 2019-11-19 17:33:12
categories: 
- iOS
- Swift
tags: 
- Currying
---

## 柯里化

**柯里化(Currying)**又译为**卡瑞化**或**加里化**，是把接受多个参数的函数变换成接受一个单一参数的函数，并且返回接受余下的参数而且返回结果的新函数的技术。 简单来说，就是把带有多个参数的函数变换成带有一个参数的函数，这样可以简化我们的函数，使其更加简洁。另外，`Swift`一直以**函数式编程**的思想著称，而**柯里化**恰好与`Swift`的编程思想契合。

比如，下面的函数可以接收*1个参数*，然后*加1*后返回结果：

```
func addOne(_ adder: Int) -> Int {
	return adder + 1
}

let result = addOne(5)
print(result) // 6
```

这样的函数**局限性**较大，如果我们将加其他数字的话，就比较不方便，当然，我们也可以用常规的方式，传入两个参数，那样就是多参数了，如果我们想只要传入一个参数，并且可以进行扩展，就可以用**柯里化函数**。例如：

```
func add(_ adder: Int) -> (Int) -> Int {
	return {
		num in
		return num + adder
	}
}

let addTwo = add(2)
let result1 = addTwo(6) // 6 + 2 
print(result1) // 8

let addThree = add(3)
let result2 = addThree(7) // 7 + 3
print(result2) // 10
```

在上面的例子中，`addTwo`就是分别是传入第一个参数后`（2）`返回的`新函数`，当调用`addTwo`之后传入参数`6`，就完成了函数的调用，最后返回的就是`6 + 2 = 8`即我们想要的结果。这样，我们就将*多参数*的形式简化成了*单参数*并且对函数进行了扩展。

当然，在`Swift`中，上面的函数还可以有更简洁的写法：

```
func add(_ adder: Int) -> (Int) -> Int {
	return {$0 + adder}
}
```

## 将 protocol 的方法声明为 mutating

为什么要将`协议（protocol）`的方法声明为`mutating`，可以先看一个例子：

```

```