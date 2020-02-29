---
title: 【iOS】真机调试,The executable was signed with invalid entitlements
date: 2020-2-27
categories:
- iOS
- Objective-C
tags:
- 真机调试报错
- entitlements
---

## 说明

昨天新创建了一个`Demo`工程，准备测试`iOS`的`Access WiFi Information`权限功能，使用`iPhone X`真机调试的时候，发现根本没法运行，于是将`Access WiFi Information`权限关掉，并且删除了之前打开`Access WiFi Information`权限而自动生成的`xxx.entitlements`权限证书文件，发现还是一样的报错。在一个新工程上面真机调试竟然报错？到底是哪里出了问题呢？

经过一天的调试，尝试了各种能想到的办法，以及在网上百度了一些资料，仍然没有解决.....

错误信息如下

```
The executable was signed with invalid entitlements.

The entitlements specified in your application’s Code Signing Entitlements file are invalid, 
not permitted, or do not match those specified in your provisioning profile. (0xE8008016).
```

错误信息截图

![error.png](https://i.loli.net/2020/02/27/qfeY1iA3QUBDgZr.png)

今天调整心态，使用`Google`查找，终于在**stack overflow**找到了问题所在

答案链接：[Entitlements file do not match those specified in your provisioning profile.(0xE8008016)](https://stackoverflow.com/a/54902044)

答案截图

![answer.png](https://i.loli.net/2020/02/27/rW9oAV4Gd6KOmpY.png)

原来是项目（这里就以我的`demo`工程名为例）的**wifidemo**与**wifidemoTests**所选择的开发者团队名不一样所致，看了一下自己的工程，的确不同，于是按照这个回答将其改为一直就好了。

**图一**

![wifidemo.png](https://i.loli.net/2020/02/27/MO4PSyeAnjXCcup.png)

**图二**
![wifidemoTests.png](https://i.loli.net/2020/02/27/en9GZPgtfXQkrOM.png)

注：*图一*和*图二*红框中的`Team`请保持一致，即可解决以上出现的问题。
