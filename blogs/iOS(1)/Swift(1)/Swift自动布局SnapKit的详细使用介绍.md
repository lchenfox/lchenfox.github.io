---
title: Swift自动布局SnapKit的详细使用介绍
date: 2017-04-27 
categories: 
- iOS
- Swift
tags: 
- Autolayout
- Snapkit
- 自动布局
- UI
---

### 简介
SnapKit，一个经典的**Swift**版的第三方库，专门用于项目的自动布局，目前在github上的**stars**就高达9340颗星，这是一个不小的数字，亦足以证明它存在的非凡意义和作用。作者认为，在iOS开发（swift）中，它是用于项目最优秀的自动布局的必选库之一。它的作者仍然是写Objective-C的第三方库Masonry的大牛 - **@Robert Payne**，开门见山，本文将详细介绍介绍**SnapKit**的详细使用和安装，相信对于初入门该库的开发者或许会有一定的帮助，当然，鉴于作者能力有限，如有不足之处，欢迎指点和批评。也可以通过加作者创建的iOS(Swift)开发互助群交流和学习，群号QQ：558179558。

### Snapkit的安装（CocoaPods)

#### 环境配置要求： 

* iOS 8.0 / Mac OS X 10.11+ 

* Xcode 8.0+

* Swift 3.0+

#### 安装

在已经安装CocoaPods的前提下， 即可以进行下列步骤。

* 在你的项目工程里的**Podfile**文件里面添加

```
source 'https://github.com/CocoaPods/Specs.git'

platform :ios, '10.0'

use_frameworks!

target '这里是你的工程名称' do
    pod 'SnapKit', '~> 3.0'
end
```

* 老生常谈，运行CocoaPods的如下命令

```
pod install
```


到此，不出意外的话，你已经将**SnapKit**集成到你的项目中了。然后，就开始讲怎么使用它了。



### Snapkit的布局使用



**1**、 实现一个宽高为100，居于当前视图的中心的视图布局，示例代码如下

```
import UIKit
import SnapKit

class ViewController: UIViewController {
    override func viewDidLoad() {
        super.viewDidLoad()
     
        let testView = UIView()
        testView.backgroundColor = UIColor.cyan
        view.addSubview(testView)
        testView.snp.makeConstraints { (make) in
            make.width.equalTo(100)         // 宽为100
            make.height.equalTo(100)        // 高为100
            make.center.equalTo(view)       // 位于当前视图的中心
        }
        
    }
}
```

更简洁的写法可以

```
import UIKit
import SnapKit

class ViewController: UIViewController {
    override func viewDidLoad() {
        super.viewDidLoad()
     
        let testView = UIView()
        testView.backgroundColor = UIColor.cyan
        view.addSubview(testView)
        testView.snp.makeConstraints { (make) in
            make.width.height.equalTo(100)    // 链式语法直接定义宽高
            make.center.equalToSuperview()    // 直接在父视图居中
        }
        
    }
}
```

效果图

![TestPicture1](http://upload-images.jianshu.io/upload_images/1974698-e0670758f7c9b415.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

**2**、View2位于View1内， view2位于View1的中心， 并且距离View的边距的距离都为20

```
import UIKit
import SnapKit

class ViewController: UIViewController {
    override func viewDidLoad() {
        super.viewDidLoad()
     
         // 黑色视图作为父视图
         let view1 = UIView()
         view1.frame = CGRect(x: 0, y: 0, width: 300, height: 300)
         view1.center = view.center
         view1.backgroundColor = UIColor.black
         view.addSubview(view1)
         
         // 测试视图
         let view2 = UIView()
         view2.backgroundColor = UIColor.magenta
         view1.addSubview(view2)
         view2.snp.makeConstraints { (make) in
         	  make.top.equalToSuperview().offset(20)      // 当前视图的顶部距离父视图的顶部：20（父视图顶部+20）
         	  make.left.equalToSuperview().offset(20)     // 当前视图的左边距离父视图的左边：20（父视图左边+20）
              make.bottom.equalToSuperview().offset(-20)  // 当前视图的底部距离父视图的底部：-20（父视图底部-20）
              make.right.equalToSuperview().offset(-20)   // 当前视图的右边距离父视图的右边：-20（父视图右边-20）
         }
        
    }
}
```

更简洁的写法

```
import UIKit
import SnapKit

class ViewController: UIViewController {
    override func viewDidLoad() {
        super.viewDidLoad()
     
         // 黑色视图作为父视图
         let view1 = UIView()
         view1.frame = CGRect(x: 0, y: 0, width: 300, height: 300)
         view1.center = view.center
         view1.backgroundColor = UIColor.black
         view.addSubview(view1)
         
         // 测试视图
         let view2 = UIView()
         view2.backgroundColor = UIColor.magenta
         view1.addSubview(view2)
         view2.snp.makeConstraints { (make) in
            make.edges.equalToSuperview().inset(UIEdgeInsets(top: 20, left: 20, bottom: 20, right: 20))
         }
        
    }
}
```

效果图

![TestPicture2](http://upload-images.jianshu.io/upload_images/1974698-ad6e16114189a9bb.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

**3**、布局一个视图view2， 让它的水平中心线小于等于另一个视图view2的左边，可以这样布局

```
import UIKit
import SnapKit

class ViewController: UIViewController {
    override func viewDidLoad() {
        super.viewDidLoad()
     
         // 黑色视图作为父视图
         let view1 = UIView()
         view1.frame = CGRect(x: 0, y: 0, width: 300, height: 300)
         view1.center = view.center
         view1.backgroundColor = UIColor.black
         view.addSubview(view1)
        
         // 测试视图
         let view2 = UIView()
         view2.backgroundColor = UIColor.magenta
         view1.addSubview(view2)
         view2.snp.makeConstraints { (make) in
            // 让顶部距离view1的底部为10的距离
            make.top.equalTo(view1.snp.bottom).offset(10)
            // 设置宽、高
            make.width.height.equalTo(100)
            // 水平中心线<=view1的左边
            make.centerX.lessThanOrEqualTo(view1.snp.leading)
         }
        
    }
}
```

效果图

![TestPicture](http://upload-images.jianshu.io/upload_images/1974698-fda525f8d8d474b0.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

### 视图的属性说明
通过上面的大致简单布局我们对SnapKit有了一个基本的了解，那么， 它的布局属性是怎么来的呢？和原生的布局类有什么关联？ 下面看一个SnapKit的布局属性表

![](http://upload-images.jianshu.io/upload_images/1974698-98e6cb55d8b7702c.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

从表中，我们知道，Snapkit的布局属性都是源自于系统的NSLayoutAttribute，那么，NSLayoutAttribute是个什么呢？其实，它在swift中是一个枚举，内部列举了很多布局属性诸如top、left、leading、centerX等，Snapkit的布局属性与它们都存在一一的对应关系。

#### Snapkit 的 greaterThanOrEqualTo 属性

如果想让视图View2的左边>=父视图View1的左边， 这时我们就可以用到greaterThanOrEqualTo

```
import UIKit
import SnapKit

class ViewController: UIViewController {
    override func viewDidLoad() {
        super.viewDidLoad()
        
         // 黑色视图作为父视图
         let view1 = UIView()
         view1.frame = CGRect(x: 0, y: 0, width: 300, height: 300)
         view1.center = view.center
         view1.backgroundColor = UIColor.black
         view.addSubview(view1)
        
         // 测试视图
         let view2 = UIView()
         view2.backgroundColor = UIColor.magenta
         view1.addSubview(view2)
         view2.snp.makeConstraints { (make) in
            // 让顶部距离view1的底部为10的距离
            make.top.equalTo(view1.snp.bottom).offset(10)
            // 设置宽、高
            make.width.height.equalTo(100)
            // 水平中心线<=view1的左边
            make.left.greaterThanOrEqualTo(view1)
            
            // 或者, 和上面一行代码一样的效果
//            make.left.greaterThanOrEqualTo(view1.snp.left)
         }
    }
}
```

效果图

![TestPicture](http://upload-images.jianshu.io/upload_images/1974698-4a58ce03c79222d3.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

其实，greaterThanOrEqualTo这个属性有点多余，比如上面这行代码 **make.left.greaterThanOrEqualTo(view1)** ， 我们可以换成 **make.left.equalToSuperview()**或**make.left.equalTo(view1.snp.left)**， 效果是一样的，也就是说，一般情况下 **>=** 或 **<=** 我们都可以直接用**equalTo**来代替！

#### SnapKit的greaterThanOrEqualTo和lessThanOrEqualTo联合使用
当我们想要让某个视图的**width或height**大于等于某个特定的值，小于等于某个特定的值的时候，一般而言，Snapkit会以greaterThanOrEqualTo为准，这里举一个width的例子，为了方便，这里指贴出了viewDidLoad中的代码（其他没必要）

```
 // 黑色视图作为父视图
 let view1 = UIView()
 view1.frame = CGRect(x: 0, y: 0, width: 300, height: 300)
 view1.center = view.center
 view1.backgroundColor = UIColor.black
 view.addSubview(view1)
    
 // 测试视图
 let view2 = UIView()
 view2.backgroundColor = UIColor.magenta
 view1.addSubview(view2)
 view2.snp.makeConstraints { (make) in
    make.width.lessThanOrEqualTo(300)
    make.width.greaterThanOrEqualTo(200)
    make.height.equalTo(100)
    make.center.equalToSuperview()
 }
```

接着，我们来看一下效果图

![Test6](http://upload-images.jianshu.io/upload_images/1974698-09c6303f2d50331c.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

很明显，最后的宽度是以**make.width.greaterThanOrEqualTo(200)**为标准的，也可以这样的，在同时使用两者的情况下，**greaterThanOrEqualTo**的优先级略比**lessThanOrEqualTo**的优先级高。值得一提的是， 在上面的例子中，如果我们只设置**make.width.lessThanOrEqualTo(300)**，那么view2是不会显示出来的，因为view2不知道你要表达的是要显示多少，小于等于300，到底是100还是200呢？(这里指针对width和height）所以它不能确定这个约束的值，但是，如果我们单独设置**make.width.greaterThanOrEqualTo(200)**，那么就和上面的效果一样，因为它会以200为标准布局约束！

#### lessThanOrEqualTo的用于上、下、左、右
如果我们想要视图view2的左边 <= view1.left + 10, 那么就可以直接用到**lessThanOrEqualTo**布局了，看下面这个例子

```
 // 黑色视图作为父视图
 let view1 = UIView()
 view1.frame = CGRect(x: 0, y: 0, width: 300, height: 300)
 view1.center = view.center
 view1.backgroundColor = UIColor.black
 view.addSubview(view1)
    
 // 测试视图
 let view2 = UIView()
 view2.backgroundColor = UIColor.magenta
 view1.addSubview(view2)
 view2.snp.makeConstraints { (make) in
    make.left.lessThanOrEqualTo(20)		// <= 父视图的左边+20
    make.right.equalTo(-40)				// = 父视图的右边-40
    make.height.equalTo(100)
    make.center.equalToSuperview()
 }
```

效果图

![Test7](http://upload-images.jianshu.io/upload_images/1974698-f85ad9126140abae.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

#### Snapkit布局的灵活性
* Snapkit布局灵活性很强， 我们看下面的例子, 他们的效果是一样的

```
make.left.equalToSuperview().offset(10)
make.left.equalTo(10)
make.left.equalTo(view1.snp.left).offset(10)
```

* 设置视图的大小（width，height）,他们效果是一样的

```
make.width.height.equalTo(100)
或
make.width.equalTo(100)
make.height.equalTo(100)
或
make.size.equalTo(CGSize(width: 100, height: 100))
```

* 优先级(priority)

我们来看一下Snapkit的优先级设置， 优先级都是附加在约束链的末尾处，看下面的使用方法

```
// 黑色视图作为父视图
let view1 = UIView()
view1.frame = CGRect(x: 0, y: 0, width: 300, height: 300)
view1.center = view.center
view1.backgroundColor = UIColor.black
view.addSubview(view1)
    
// 测试视图
let view2 = UIView()
view2.backgroundColor = UIColor.magenta
view1.addSubview(view2)
view2.snp.makeConstraints { (make) in
	make.width.equalTo(100).priority(666)
	make.width.equalTo(250).priority(999)
	make.height.equalTo(111)
	make.center.equalToSuperview()
}
```

效果图

![priorityimage](http://upload-images.jianshu.io/upload_images/1974698-87a1f04f3651a3a8.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

从上面我们可以知道, 我们设置了两个优先级：make.width.equalTo(100).priority(666) **和** make.width.equalTo(250).priority(999)， 那运行结果是一个哪个为准呢？显然是以优先级为 **999**的为准，因为 priority(999)>priotity(666)， 所以在使用Snapkit的过程中，有时我们可以使用优先级priority来设置我们的约束， 另外，值得一提的是，SnapKit的优先级最大值只能是 1000， 如果优先级的数值超过1000，则运行时就会**Crash**！这里要尤其注意。

</br>

* 更新约束（引用约束）

</br>

我们可以通过保存某一个约束布局来更新相应的约束，或者保存一组约束布局到一个数组中更新约束， 具体看下面代码

```
// 保存约束（引用约束）
var updateConstraint: Constraint?
    
override func viewDidLoad() {
    super.viewDidLoad()
    
    // 黑色视图作为父视图
    let view1 = UIView()
    view1.frame = CGRect(x: 0, y: 0, width: 300, height: 300)
    view1.center = view.center
    view1.backgroundColor = UIColor.black
    view.addSubview(view1)

    // 测试视图
    let view2 = UIView()
    view2.backgroundColor = UIColor.magenta
    view1.addSubview(view2)
    view2.snp.makeConstraints { (make) in
        make.width.height.equalTo(100)  // 宽高为100
        self.updateConstraint = make.top.left.equalTo(10).constraint   // 距离父视图上、左为10
    }
    
    let updateButton = UIButton(type: .custom)
    updateButton.backgroundColor = UIColor.brown
    updateButton.frame = CGRect(x: 100, y: 80, width: 50, height: 30)
    updateButton.setTitle("更新", for: .normal)
    updateButton.addTarget(self, action: #selector(updateConstraintMethod), for: .touchUpInside)
    view.addSubview(updateButton)
}
    
// 更新约束
func updateConstraintMethod() {
    self.updateConstraint?.update(offset: 50)   // 更新距离父视图上、左为50
}
```

![testgif1](http://upload-images.jianshu.io/upload_images/1974698-5d2e213fdaa51b25.gif?imageMogr2/auto-orient/strip)

* 更新约束(snp.updateConstraints)

说起这个`updateConstraints`, 我也懵逼过，那么它到底有何作用呢？又怎么用呢？它和一开始就使用的`makeConstraints`又有什么明确的区别呢？请继续往下看

**说明1**：如果你这是更新某个约束或某几个约束的常量值，你就可以使用`updateConstraints`而不是`makeConstraints`。

**说明2**：这个也是苹果推荐用来添加或更新约束的方式

**说明3**：这个方法可以调用多次，会相应`setNeedsUpdateConstraints`, 在控制器中，可以写在`override func updateViewConstraints()`方法里面（当然也可以写在你想要什么时候更新的点击事件里面）

```
import UIKit
import SnapKit

class ViewController: UIViewController {

    lazy var blackView = UIView()
    
    override func viewDidLoad() {
        super.viewDidLoad()
        
        blackView.backgroundColor = UIColor.black
        view.addSubview(blackView)
        blackView.snp.makeConstraints { (make) in
            
            // 四个约束确定位置和大小
            make.width.equalTo(100)
            make.height.equalTo(150)
            make.top.equalTo(10)
            make.centerX.equalToSuperview()
        }
 
    }
    
    override func updateViewConstraints() {
        
        blackView.snp.updateConstraints { (make) in
            // 更新距离父视图顶部的约束（从 10 ---> 300 ）
            make.top.equalTo(300)
        }
        
        // 根据苹果，调用父类应该放在末尾
        super.updateViewConstraints()
    }
}
```

`注意`: 从上面的代码中我们很明确地知道， `blackView`通过`width`、`height`、`top`、`centerX`确定了它本身的大小和位置， 但是， 在 run 出来之后，它的top改变了距离， 从 10 变成了 300，其他三个约束保持不变， 见下图效果：

![test10](http://upload-images.jianshu.io/upload_images/1974698-f26b2286347c39cc.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

显而易见， 除了`top`约束， 其他都没有改变！ 也就是说，约束被更新（相当于系统升级一样，是一个道理）

现在，我们通过UIButton的点击事件来证明一下制作约束`makeConstraints`和`updateConstraints`**具体的区别**在哪里？

```
lazy var blackView = UIView()
    
override func viewDidLoad() {
    super.viewDidLoad()
    
    blackView.backgroundColor = UIColor.black
    view.addSubview(blackView)
    blackView.snp.makeConstraints { (make) in
        
        make.width.equalTo(100)
        make.height.equalTo(150)
        make.top.equalTo(50)
        make.centerX.equalToSuperview()
    }
    
    let btn = UIButton(type: .custom)
    btn.backgroundColor = UIColor.brown
    btn.frame = CGRect(x: 100, y: 200, width: 60, height: 30)
    btn.addTarget(self, action: #selector(buttonAction), for: .touchUpInside)
    view.addSubview(btn)
 
}
    
// 点击更新/制作约束
func buttonAction() {
    
    blackView.snp.makeConstraints { (make) in
        make.width.height.equalTo(20)
        make.top.equalTo(300)
    }
    
}
```

先看效果图

**点击事件发生前(图1）：**

![test11](http://upload-images.jianshu.io/upload_images/1974698-9a709cc7bfd4a9e9.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

**点击事件发生后（图2）**

![test12](http://upload-images.jianshu.io/upload_images/1974698-e5f347e060f611f2.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

**图3**

![test14](http://upload-images.jianshu.io/upload_images/1974698-a8eb71769d210970.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

**图4**

![test13](http://upload-images.jianshu.io/upload_images/1974698-ca57e8436580500d.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

>上面，我们知道，视图 **blackView**一开始是由四个约束确定位置和大小，在点击事情响应后，我们又给 **blackView** 制作（记住，是制作，而不是重做，两者有明确的区别）了3个约束，分别是 `width`、`height`、`top`, 那么这样做问题出现在哪里呢？ **第一**， 点击事情发生前（图1）， 在点击事件发生后（见图2）， 我们发现，`blackView `的`width`、`height`约束改变了，但是 `top`却没有改变，还是原来的距离父视图顶部 50 的距离， 原因在于，我们在原来的约束基础上又添加了**多余的**约束， 也就是说，约束从4个变成了7个（见图3左边constraints）， 这样就产生了约束不明确，进而导致**snapkit**的警告（见图4）， 这样布局显然是不可取的，在项目中这样做极其危险，甚至可能会导致异常奔溃！！！！

**现在**， 我们该将点击事件中的约束布局从`makeConstraints `改变成`updateConstraints`来试试两者有什么区别(下面只添加了点击事件的代码，其他事重复的就不多此一举了）

```
func buttonAction() {
    // 注意这里是updateConstraints， 而不是makeConstraints
    blackView.snp.updateConstraints { (make) in
        make.width.height.equalTo(20)
        make.top.equalTo(300)
    }
    print("这里试试snapkit有没有报警告")
}

```

接着看点击事件后的效果图

**图5**

![test5](http://upload-images.jianshu.io/upload_images/1974698-d3ad7232e8a70246.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

**图6**

![test6](http://upload-images.jianshu.io/upload_images/1974698-a0d9f2b52521f740.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

**图7**

![test7](http://upload-images.jianshu.io/upload_images/1974698-1e9f584ce592812c.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

>发现没有，在将`makeConstraints `改变成`updateConstraints`之后，约束还是4个，snapkit没有报警告，点击事件中的`width`、`height`、`top`全部起了作用，而这就是两者的**本质区别**：`makeConstraints `是制作约束，在原来的基础上再添加另外的约束，也就是画蛇添足，约束增加，视图布局就有不确定性，从而有些约束起作用，有些不起作用（如上面的top），snapkit报警告！！！而`updateConstraints`是更新约束，改变原有约束，约束不会增加，没经过`updateConstraints`处理的保持原有约束，经过处理就更新约束，约束不会减少，snapkit不会产生警告，这是正常标准的更新约束的正确方式！！！

</br>

* 重做约束（remakeConstraints）

重做约束的本质就是：去掉已有的**所有约束**， 重新做约束，记住，是做约束， 也就是说， 使用了`remakeConstraints `后，重做的约束必须要能确定相应视图的**大小**和**位置**, 之前`makeConstraints `的约束已经不会存在了，完全销毁！！！

```
// 点击更新/制作约束
func buttonAction() {
    
    // 注意这里是 remakeConstraints
    blackView.snp.remakeConstraints { (make) in
        make.width.height.equalTo(20)
        make.top.equalTo(300)
    }
    
    print("这里试试snapkit有没有报警告")
}
```

**效果图**

**图（1）**

![test18](http://upload-images.jianshu.io/upload_images/1974698-5fd5a69571c14dd0.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

**图（2）**

![test19](http://upload-images.jianshu.io/upload_images/1974698-d5f4714041562066.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

**图（3）**

![test20](http://upload-images.jianshu.io/upload_images/1974698-bbf3ddc34397a09b.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

>我们看到， `blackView`重做了约束， 之前的约束不起任何作用，由于它在重做约束后只有 3 个约束分别是 `width`、`height`、`top`, 但是这里有一个问题，就是这 3 个约束只能确定大小，无法确定视图的位置， 所以在水平方向上或者左右缺少一个布局条件， 故 `blackView`整体视图的x紧靠左边（默认）！ 另外我们发现， 在**图（3）**中，右上角出现了一个感叹号“！”, 那是因为告诉你缺少了一个约束条件：`x-xcode-debug-views://7f81fcbc7900: runtime: Layout Issues: Horizontal position is ambiguous for UIView.
`

#### 小结

通过以上学习，我们或深或浅地学习了布局三方库SnapKit的使用， 我相信，只要将上述布局会使用，并且懂得布局的原则和道理，基本上就可以“高枕无忧”了，至于涉及动态布局、动画布局等知识，后续有时间会更新文档。

