---
title: Building for iOS Simulator, but the linked and embedded framework 'IdcardQuality.framework' was built for iOS
date: 2020-12-05
categories:
- iOS 
tags:
- iOS simulator
- Linked and embedded framework 
---

## 错误

在使用百度`OCR`时，无法使用模拟器调试（`Xcode Version 12.2 (12B45b)`)，这是由于百度`OCR`库不支持模拟器架构，报错如下：

***Building for iOS Simulator, but the linked and embedded framework 'IdcardQuality.framework' was built for iOS.***

**截图：**

![error.png](https://i.loli.net/2020/11/30/hrO7Ew6eDsu259M.png)

当然，这种智能识别库都是需要真机调用相机的，模拟器无法使用也是正常。但是，我们虽然不能也无法使用模拟器，我们也希望工程能够正常跑通，以便我们能够使用模拟器来测试其他的业务逻辑，不然需要一直使用**真机**来调试是非常不方便的。

## 解决

找到`Build Settings/Build Options/Excluded Source File Names`，在`Debug`模式下，添加`Any iOS Simulator SDK`，在后面添加要在模拟器运行情况下需要排除的文件，这里是`IdcardQuality.framework`：

![resolve.png](https://i.loli.net/2020/11/30/mbQnKL5CPpjMiDv.png)

