---
title: React Native使用react-native-pushy热更新审核被拒
date: 2019-11-05 11:10:03
categories: React Native
tags: React Native, react-native-pushy, 热更新, 苹果审核
---


### 前言

***

今年，综合了一些考虑，选择了 [GitHub: react-native-pushy](https://github.com/reactnativecn/react-native-pushy) 作为热更新，在前面的两个版本使用了热更新，但是没有被拒，上线都比较顺利，直到这个版本，第二天直接被苹果拒绝！前有`WaxPatch`和`JSPatch`等热修复框架，因为会调用私有API、篡改原生代码的能力而被苹果拒之门外，接着苹果允许`React Native`的这种不会修改原生代码、只更新`js代码`和一些资源文件的热修复存在。如今，陆陆续续已经有一部分人因为使用`react-native-pushy`热更新而直接被苹果拒绝。

### 被拒原因

****

##### 理由如下：

2019年10月26日 上午12:17

发件人 Apple

2.3 Performance: Accurate Metadata

2.5 Performance: Software Requirements

Guideline 2.3.1 - Performance


We discovered that your app contains hidden features.

The next submission of this app may require a longer review time, and this app will not be eligible for an expedited review until this issue is resolved.

Next Steps

- Review the Performance section of the App Store Review Guidelines.
- Ensure your app is compliant with all sections of the App Store Review Guidelines and the Terms & Conditions of the Apple Developer Program.
- Once your app is fully compliant, resubmit your app for review.

Submitting apps designed to mislead or harm customers or evade the review process may result in the termination of your Apple Developer Program account. Review the Terms & Conditions of the Apple Developer Program to learn more about our policies regarding termination.



Guideline 2.5.2 - Performance - Software Requirements


Your app, extension, or linked framework appears to contain code designed explicitly with the capability to change your app’s behavior or functionality after App Review approval, which is not in compliance with App Store Review Guideline 2.5.2 and section 3.3.2 of the Apple Developer Program License Agreement.

This code, combined with a remote resource, can facilitate significant changes to your app’s behavior compared to when it was initially reviewed for the App Store. While you may not be using this functionality currently, it has the potential to load private frameworks, private methods, and enable future feature changes. This includes any code which passes arbitrary parameters to dynamic methods such as dlopen(), dlsym(), respondsToSelector:, performSelector:, method_exchangeImplementations(), and running remote scripts in order to change app behavior and/or call SPI, based on the contents of the downloaded script. Even if the remote resource is not intentionally malicious, it could easily be hijacked via a Man In The Middle (MiTM) attack, which can pose a serious security vulnerability to users of your app.

The next submission of this app may require a longer review time, and this app will not be eligible for an expedited review until this issue is resolved.

Next Steps

- Review the Software Requirements section of the App Store Review Guidelines.
- Ensure your app is compliant with all sections of the App Store Review Guidelines and the Terms & Conditions of the Apple Developer Program.
- Once your app is fully compliant, resubmit your app for review.

Submitting apps designed to mislead or harm customers or evade the review process may result in the termination of your Apple Developer Program account. Review the Terms & Conditions of the Apple Developer Program to learn more about our policies regarding termination.


显然，苹果的大意如下：

- 1. App隐藏了功能
- 2. 审核通过后，具备修改应用程序的行为或功能的能力
- 3. 检测到APP获取远程资源，或许会调用私有API及私有方法，利用动态特性给动态方法传递参数，如`dlopen()、dlsym()、respondsToSelector:、performSelector:、method_exchange()`，易受`MiTM`攻击，下发恶意脚本等等

回复很官方，也没有明确指出是**热更新**的原因，但是有部分人收到被拒的理由里面明确致命了使用`Hot Update`热更新。

### 过程

---

- 2019年10月24日提交

- 2019年10月25日被拒

- 2019年10月26日去掉`jenkins+fastlane`自动化打包脚本（以为是这个原因，毕竟之前的版本使用热更新没有被拒）重新打包上传到`App Store Connect`

- 截止2019年10月30日依然在审核中

- 10月30日中午去掉`iOS`的热更新，并再次打包重新上传，去掉热更新的步骤：

**（1）修改AppDelegate为**

```
    NSURL *jsCodeLocation;
#if DEBUG
    // 原来的jsCodeLocation保留在这里
    jsCodeLocation = [[RCTBundleURLProvider sharedSettings] jsBundleURLForBundleRoot:@"index" fallbackResource:nil];
#else
    // 非DEBUG情况下启用热更新
    jsCodeLocation = [[NSBundle mainBundle] URLForResource:@"main" withExtension:@"jsbundle"];
//    jsCodeLocation=[RCTHotUpdate bundleURL];  // 不要热更新
#endif
```

**（2）删除`Libraries -> RCTHotUpdate.xcodeproj`**

**（3）注释掉所有热更新的js检测代码**，如下：

```

/****************** hot update *********************/
/*
import {
    isFirstTime,
    isRolledBack,
    packageVersion,
    currentVersion,
    checkUpdate,
    downloadUpdate,
    switchVersion,
    switchVersionLater,
    markSuccess,
} from 'react-native-update';
**/

const {appKey} = _updateConfig[Platform.OS];

export default class rootApp extends Component {

    componentDidMount() {
         this._checkHotUpdateVersion();
    };

    /**
    _checkHotUpdateVersion = (tryAgain = true) => {
        checkUpdate(appKey).then(info => {
           ...
        }).catch(() => {
           ...
        });
    };
	
	......

**/
```

- 11月5日凌晨2点22分，审核通过！！！

### 小结

---

之前使用热更新没有问题，从目前大多数人由于使用热更新审核被拒的情况来看，苹果开始着手清理了，并且后续支不支持热更新也很难说，尽管一部分人还是通过了审核，但是不排除审核遗漏的情况。

初步猜测，后续苹果可能不支持热更新，尽管不修改原生代码，不调用原生私有`API`，但毕竟热更新的服务器一般是托管在第三方平台上，但是在苹果看来，只要不通过其审核的，都是不安全的。

其次，尽管只是修改`js`及一些资源文件，但是从对用户体验方面来说，苹果可能会觉得使用热更新会随意更改用户的习惯，会给用户造成体验方面的一系列问题。

最后，也是苹果觉得最危险的地方在于，担心`MiTM攻击`，另外我个人觉得，苹果还有一个想法，那就是希望开发者回归原生生态，毕竟，使用自己的语言，经过自己的审核才是苹果最希望的。

