---
title: iOS interactivePopGestureRecognizer卡住&手势滑动监听
date: 2021-3-12
categories:
- iOS 
tags:
- interactivePopGestureRecognizer
---

## 前言

手势侧滑导航在`iOS`中尤其常见，在项目中，经常和其打交道，但是经常发现，当侧滑到根控制器（第一级界面）时，继续快速使用侧滑手势`popGesture`会导致卡住。有时候，想要监听某个界面的侧滑手势是否结束来做一些其他操作，比如，在一个有**定时器（NSTimer）**的界面，使用侧滑到上一级页面，会导致该界面无法释放，这就需要知道当前侧滑手势是否已经结束，在结束时**移除（NSTimer）**等等。

## 侧滑到根控制器（第一级页面）卡住

滑动到首页（一般就是根控制器所属的第一级界面）会卡死，**这是iOS系统本身存在的一个BUG**，解决方案就是创建一个`BaseNavigationController`（继承`UINavigationController`），在`BaseNavigationController`中监听**代理方法**，判断当子视图控制器数`viewControllers`大于`1`时，允许`pop`手势可以滑动。在`BaseNavigationController.m`中，如下：

```
#pragma mark UINavigationControllerDelegate
- (void)navigationController:(UINavigationController *)navigationController didShowViewController:(UIViewController *)viewController animated:(BOOL)animated
{
    if ([self respondsToSelector:@selector(interactivePopGestureRecognizer)]) {
        // If viewController is rootViewController, disable the pop gesture recognizer.
        self.interactivePopGestureRecognizer.enabled = self.viewControllers.count > 1;
    }
}
```

## 监听`interactivePopGestureRecognizer`手势

一般我们很少会需要监听侧滑到上一级的手势的情况，这种场景可能会出现在一些特殊处理中，比如当使用侧滑手势`pop`到上一级时，由于一些操作未完成，当前`self`被持有，而导致`dealloc`方法没有被执行，换句话说，当前`view controller`没有释放造成内存泄漏，这种情况下，监听侧滑手势是否执行就有必要了。在`BaseNavigationController.m`中，如下：

```
- (void)navigationController:(UINavigationController *)navigationController willShowViewController:(UIViewController *)viewController animated:(BOOL)animated
{
    [viewController.transitionCoordinator notifyWhenInteractionChangesUsingBlock:^(id<UIViewControllerTransitionCoordinatorContext> context) {
        if (context.isCancelled) return;
        [self postPopGestureNotification];
    }];
}

- (void)postPopGestureNotification
{
    NSMutableSet *notificationNames = [NSMutableSet setWithObjects:
                                       @"FirstViewController",
                                       @"SecondViewController", nil];
   
    NSMutableSet *vcNames = [NSMutableSet set];
    for (UIViewController *vc in self.viewControllers) {
        [vcNames addObject:NSStringFromClass([vc class])];
    }
    
    [notificationNames minusSet:vcNames];

    [notificationNames enumerateObjectsUsingBlock:^(id  _Nonnull obj, BOOL * _Nonnull stop) {
        [[NSNotificationCenter defaultCenter] postNotificationName:obj object:nil];
    }];
}
```

在`- (void)navigationController:(UINavigationController *)navigationController willShowViewController:(UIViewController *)viewController animated:(BOOL)animated`中，可以使用`- (void)notifyWhenInteractionChangesUsingBlock: (void (^)(id <UIViewControllerTransitionCoordinatorContext>context))handler`监听到侧滑手势的情况，如果`context.isCancelled`为`YES`，说明用户侧滑的时候取消了滑动，即没有返回到上一级页面，反之，如果为`NO`，说明将`pop`到上一级页面，当前页面即将被释放。

> 在`iOS 10`以下，你也可以使用`- (void)notifyWhenInteractionEndsUsingBlock: (void (^)(id <UIViewControllerTransitionCoordinatorContext>context))handler`监听，不过`iOS 10`以上不建议使用，从目前情况来看，大多数项目部署最低版本都是`iOS 10`了，没有必要再使用废弃的`API`。

因此，当`context.isCancelled`为`NO`时，我们可以发送一个通知（`notification`）到当前正在操作侧滑手势`pop`的这个页面，在这个页面做监听，就可以在收到通知时，执行你想要的操作就可以了。

> 在使用过程中，需要注意的是，如果你在多个页面都需要监听侧滑手势，比如从首页 -> `FirstViewController` -> `SecondViewController`，需要在`FirstViewController`和`SecondViewController`中都监听侧滑到上一级的手势，这就会有一个问题，无论你在哪一个页面使用侧滑手势返回时，正在监听手势的页面都会收到通知，例如，你在`SecondViewController`中侧滑返回到`FirstViewController`时，`FirstViewController`中的通知也会被执行！但是，一般情况下，我们肯定不希望侧滑的时候，影响到其他界面，只希望当前界面能够收到通知即可，那么如何避免这一问题呢？如上，我的做法是，把所有你要监听的`view controller`都以文件名字符串的形式放到一个集合中，在侧滑的时候，获取到所有的`view controllers`添加到另一个集合中，然后做两者的**差集**，这样，我们就把当前`view controller`前面的`view controllers`过滤掉了，然后遍历剩下的通知名，并发送通知，这样就可以只在当前页面即`SecondViewController`收到侧滑手势的监听了。

比如，在`demo`（**文末有提供**）中，我们监听如下：

```
- (void)viewDidLoad {
    [super viewDidLoad];
    // Wait 100s to execute `timeAction`
    _timer = [NSTimer scheduledTimerWithTimeInterval:100 target:self selector:@selector(timeAction) userInfo:nil repeats:NO];
    [[NSNotificationCenter defaultCenter] addObserver:self selector:@selector(popGestureDidEnd) name:@"SecondViewController" object:nil];
}

- (void)popGestureDidEnd
{
    NSLog(@"⚠️⚠️⚠️ OOPS！SecondViewController -> popGestureDidEnd");
    [self removeTimer];
}
```

[个人博客](https://www.clcoder.com/)</br>
[Demo地址](https://github.com/lchenfox/NavigationPopGestureDemo)</br>
2021-03-12

