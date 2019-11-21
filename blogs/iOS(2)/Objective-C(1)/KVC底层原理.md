---
title: KVC底层原理
date: 2019-11-21 
categories: 
- iOS
- Objective-C
tags: KVC
---

## 前言

`KVC(Key-Value-Coding)`即`键值编码`，在`iOS`中应用及其广泛，我们通常使用`KVC`来*设置值*和*获取值*，知道`KVC`的基本运作原理对我们在日常开发中修复`bug`起到很好的作用。

## KVC之取值

#### valueForKey

首先，来看一个简单问题，创建一个`Person`类。

`Person.h`:

```
#import <Foundation/Foundation.h>

@interface Person : NSObject

@end
```

`Person.m`:

```
#import "Person.h"

@implementation Person

@end
```

在`ViewController.m`中，导入`Person.h`，`ViewController.m`内容如下：

```
#import "ViewController.h"
#import "Person.h"

@interface ViewController ()

@end

@implementation ViewController

- (void)viewDidLoad {
	[super viewDidLoad];
	[self test];
}

- (void)test
{
	Person *p = [[Person alloc] init];
	// 注意，获取的时候这个key必须是成员变量的原形！！！
	// 比如，如果这里不是name, 而是下划线 _name 那么就要就Person类
	// 里面的成员变量一定要有_name，否则就会奔溃！
	NSString *name = [p valueForKey:@"name"];
	NSLog(@"name is %@", name);
}

@end
```

在运行之前，先思考一下结果是什么？

答案是：**程序崩溃，抛出异常！**

```
*** Terminating app due to uncaught exception 'NSUnknownKeyException', 
reason: '[<Person 0x600002c35340> valueForUndefinedKey:]: this class 
is not key value coding-compliant for the key name.'
```

为什么会这样呢？我们来分析一下：`实例对象p`并没有这个`name`成员变量或者说没有这个属性，在调用`KVC`的`valueForKey`方法时，由于找不到`name`从而执行了`- (id)valueForUndefinedKey:(NSString *)key`这个方法，接着就抛出了异常，因此程序崩溃。

不信，我们来验证一下，在`Person.h`中，复写`- (id)valueForUndefinedKey:(NSString *)key`方法，于是`Person.m`的内容就变成：

```
#import "Person.h"

@implementation Person

- (id)valueForUndefinedKey:(NSString *)key {
	return @"hello, world!";
}

@end
```

再次运行，输出结果：

```
name is hello, world!
```

哇，果然如此！当使用`valueForKey`方法时，如果查找不到相应的属性或成员变量，就直接走`valueForUndefinedKey`抛出异常。

问题是，`KVC`是如何查找属性或成员变量的呢？我们来继续验证：

在`Person.h`中，定义`name`成员变量，并在`Person.m`的初始化方法中给`name`赋值。

于是，`Person.h`：

```
#import <Foundation/Foundation.h>

@interface Person : NSObject
{
	NSString *name;
}
@end
```

`Person.m`:

```
#import "Person.h"

@implementation Person

- (instancetype)init
{
	self = [super init];
	if (self) {
		name = @"name";
	}
	return self;
}

@end
```

运行程序，结果是：

```
name is name
```

一点也不意外，按正常的逻辑执行，我们再添加一个`_name`成员变量并赋值

`Person.h`：

```
#import <Foundation/Foundation.h>

@interface Person : NSObject
{
	NSString *name;
	NSString *_name;
}
@end
```

`Person.m`:

```
#import "Person.h"

@implementation Person

- (instancetype)init
{
	self = [super init];
	if (self) {
		_name = @"_name";
		name = @"name";
	}
	return self;
}

@end
```

运行程序，结果是什么呢？

结果是：

```
name is _name
```

到这里，我们可以确定**`_name`的优先级比`name`的优先级要高。**

同理，我们可以添加成员变量`_isName`和`isName`，同上面比较的方法一样比较（这里就不继续演示了），可以发现成员变量的查找优先级如下：

```
_name > _isName > name > isName
```

也就是说，**在`KVC`中，查找成员变量或属性的方式是，首先查找`_key`，如果找不到，继续查找`_isKey`，再找不到，继续查找`key`，还是查找不到，才查找`isKey`，如果`isKey`都找不到的话，程序将抛出异常（除非你重写了`- (id)valueForUndefinedKey:(NSString *)key`方法）。**

---

我们知道，在`Objective-C`中，有一个东西叫`属性`，而`属性`的本质就是对`成员变量`、`setter`、`getter`方法的封装，那么当我们通过`KVC`调用`valueForKey`方法获取实例变量的时候，而获取的方式很类似`getter`，因为`getter`就表示获取的意思，那么，它与`getter`方法有什么关系呢？

`Person.h`：

```
#import <Foundation/Foundation.h>

@interface Person : NSObject
{
	NSString *isName;
	NSString *_isName;
	NSString *name;
	NSString *_name;
}
@end
```

`Person.m`:

```
#import "Person.h"

@implementation Person

- (instancetype)init
{
	self = [super init];
	if (self) {
		_isName = @"init~_isName";
		_name = @"init~_name";
		name = @"init~name";
		isName = @"init~isName";
	}
	return self;
}

- (NSString *)getName
{
	return @"getName";
}

- (NSString *)name
{
	return @"name";
}

- (NSString *)isName
{
	return @"isName";
}

- (NSString *)_getName
{
	return @"_getName";
}

- (NSString *)_name
{
	return @"_name";
}

@end
```

运行程序，输出结果如下：

```
name is getName
```

很明显，当通过`valueForKey`获取`name`成员变量，若有`- (NSString *)getName`方法存在，则优先执行这个方法，并且不再去获取实例变量在`- (instancetype)init`的初始化值。另外值得注意的是，执行了`- (NSString *)getName`方法，说明其优先级大于`- (NSString *)name`、`- (NSString *)isName`、`- (NSString *)_getName`、`- (NSString *)_name`方法。

我们去掉`- (NSString *)getName`方法，再次运行，输出结果如下：

```
name is name
```

接着去掉`- (NSString *)name`方法，输出结果如下：

```
name is isName
```

接着去掉`- (NSString *)isName`方法，输出结果如下：

```
name is _getName
```

接着去掉`- (NSString *)_getName`方法，输出结果如下：

```
name is _name
```

结果表明，只要`- (NSString *)getName`、`- (NSString *)name`、`- (NSString *)isName`、`- (NSString *)_getName`、`- (NSString *)_name`中的任何一个方法存在，就不再去获取初始化中的`name`成员变量的值。

也就是说，在`KVC`中，只要存在`- (NSString *)getKey`、`- (NSString *)key`、`- (NSString *)isKey`、`- (NSString *)_getKey`、`- (NSString *)_key`方法，其优先级都比成员变量高，其中优先级顺序是：`- (NSString *)getKey` > `- (NSString *)key` > `- (NSString *)isKey` > `- (NSString *)_getKey` > `- (NSString *)_key`。

> 事实上，`- (NSString *)getKey` > `- (NSString *)key`、`- (NSString *)isKey`、`- (NSString *)_getKey` 、 `- (NSString *)_key`在底层本质上都是`getter`方法，就如同查找成员变量一样，`Apple`提供了多种获取方式。

还有一点要特别注意，当没有以上的`相关方法`时，我们知道`valueForKey`会去查找实例变量的值，但是，在查找实例变量之前，其实还执行了一个方法，它是`+ (BOOL)accessInstanceVariablesDirectly`并且其默认返回值为`YES`，如果既没有上面的`gettter`方法，并且`+ (BOOL)accessInstanceVariablesDirectly`的返回值为`NO`，那么将会直接走`- (id)valueForUndefinedKey:(NSString *)key`抛出异常，程序崩溃。当然，一般情况下，这个方法我们不会使用到。

流程图如下：

![KVC .png](https://i.loli.net/2019/11/20/VjYRdXILghfma6Z.png)

#### valueForKeyPath

事实上，`KVC`除了提供`valueForKey`方法来获取值之外，还提供了`valueForKeyPath`的取值方法，区别在于`valueForKeyPath`可以*按路径取值*，比如：假设`person`实例对象有一个`dog`属性，`dog`自身又存在一个`name`属性，那么我们可以使用`valueForKeyPath`的取值方法来获取`dog`的`name`属性

```
id dogName = [person valueForKeyPath: @"dog.name"]
```

这个原理差不多，就不赘述了。

## KVC之设值

既然`KVC`可以获取值，反之，也一定可以设置值，从上面我们可以知道，`KVC`的取值的成员变量有`4`个，并且有着优先级，即：`_key` > `_isKey` > `key` > `isKey`。那么，同样的，当我们使用`KVC`的`setValue:value forKey:key`方法时，**`KVC`也会去查找相应的`4`个成员变量，并且设置值的优选级完全和`KVC`取值一样！（前提条件是没有相应的`setter`方法，这个稍后会讲）**

同样地，我们来测试一下：

`Person.h`中：

```
#import <Foundation/Foundation.h>

@interface Person : NSObject
{
	NSString *_name;
	NSString *_isName;
	NSString *name;
	NSString *isName;
}
- (void)printName;
@end
```

`Person.m`中：

```
#import "Person.h"

@implementation Person

- (void)printName
{
	NSLog(@"_name: %@", _name);
	NSLog(@"_isName: %@", _isName);
	NSLog(@"name: %@", name);
	NSLog(@"isName: %@", isName);
}
@end
```

`ViewController.m`中：

```
#import "ViewController.h"
#import "Person.h"

@interface ViewController ()

@end

@implementation ViewController

- (void)viewDidLoad {
	[super viewDidLoad];
	[self test];
}

- (void)test
{
	Person *p = [[Person alloc] init];
	[p setValue:@"test name" forKey:@"name"];
	[p printName];
}

@end
```

执行程序，结果如下：

```
_name: test name
_isName: (null)
name: (null)
isName: (null)
```

可以看出，先设置的是`_name`的值，去掉`_name`，就会设置`_isName`的值，其它同理。

需要注意的是，如果设置的成员变量不存在，那么程序将调用`- (void)setValue:(id)value forUndefinedKey:(NSString *)key`抛出异常，直接奔溃，这个和`KVC`取值时找不到`getter`方法、成员变量时调用`- (id)valueForUndefinedKey:(NSString *)key`是一样的道理。

同理，`KVC`取值有`+ (BOOL)accessInstanceVariablesDirectly`方法，`KVC`设值也是同一个方法，若没有相应的`setter`方法，并且返回`NO`时，程序将调用`- (void)setValue:(id)value forUndefinedKey:(NSString *)key`抛出异常，因此，为了防止异常，我们有时候可以重写`- (void)setValue:(id)value forUndefinedKey:(NSString *)key`方法以防止奔溃。另外，值得一提的是，`KVC`在给数值型数据类型如`NSInteger`设值`nil`值的话，会调用`- (void)setNilValueForKey:(NSString *)key`抛出异常。比如：

在`Person.h`中：

```
{
	NSInteger age;
}
```

在`ViewController.m`中：

```
Person *p = [[Person alloc] init];
[p setValue:nil forKey:@"age"];
```

毫无疑问，程序将奔溃！因此，仅仅重写`- (void)setValue:(id)value forUndefinedKey:(NSString *)key`方法是不够的的，要同时重写`- (void)setNilValueForKey:(NSString *)key`，防止程序奔溃！

在`KVC`取值中，有`5`个`getter`方法，但是在`KVC`设值中，只有`2`个`setter`方法，分别是：`setKey`、`setIsKey`，例如

```
- (void)setName:(NSString *)name
{
	NSLog(@"setter name: %@", name);
}

- (void)setIsName:(NSString *)name
{
	NSLog(@"setter isName: %@", name);
}

```

优先级：`setName` > `setIsName`，也即是 `setKey` > `setIsKey`。

和`KVC`取值是一个道理，若有以上所说的`2`个`setter`方法，则优先执行`setter`方法，忽略成员变量在初始化时的值。没有`setter`方法，就判断`+ (BOOL)accessInstanceVariablesDirectly`的返回值是否为`NO`，若为`NO`，调用`- (void)setValue:(id)value forUndefinedKey:(NSString *)key`抛出异常（没有重写该方法则崩溃），若为`YES`，就去查找相应的成员变量并赋值。

## 手动写一个KVC

参照`KVC`的设值和取值的基本流程，我们也可以自己手动写一个属于自己的`KVC`。具体说明这里就不介绍了，有兴趣可直接查看源码，都是一些简单的业务逻辑判断，利用`OC`的`runtime机制`，实现出相应的`KVC`效果。


[查看KVC源码](https://github.com/lchenfox/OC_KVCDemo)



