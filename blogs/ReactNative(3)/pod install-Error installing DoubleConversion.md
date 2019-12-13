---
title: RN之[pod install] Error installing DoubleConversion
date: 2019-12-13 14:17:05
categories: React Native
tags: 
- React Native
- pod install
- 编译库
- DoubleConversion
- Folly
- glog
- Error installing DoubleConversion
---

## 为什么要写这篇文章？

在`RN`工程中，当我们使用`cocoapods`来安装一些`RN`必须的第三方编译库（比如`Folly`、`boost`），由于这些编译库是在国外，并且无论翻墙与否，很多时候直接使用`cocoapods`来自动下载安装基本都会失败，最近尝试在原来的`RN`工程和新建的`RN`工程上面都做了尝试，结果尝试了`3`天，基本都是失败的，报错的原因都是一样，这样的话，项目根本没法运行。因此，在这种自动安装经常失败甚至一直失败的情况下，了解手动下载安装就必不可少了。

#### 报错：

```
➜  ios pod install
Analyzing dependencies
Fetching podspec for `DoubleConversion` from `../node_modules/react-native/third-party-podspecs/DoubleConversion.podspec`
Fetching podspec for `Folly` from `../node_modules/react-native/third-party-podspecs/Folly.podspec`
Fetching podspec for `glog` from `../node_modules/react-native/third-party-podspecs/glog.podspec`
Downloading dependencies
Installing DoubleConversion (1.1.6)

[!] Error installing DoubleConversion
[!] /usr/local/bin/git clone https://github.com/google/double-conversion.git /var/folders/mn/7jdnkyy91_n0bxkrgjgjk17c0000gn/T/d20191212-36568-33nxni --template= --single-branch --depth 1 --branch v1.1.6

Cloning into '/var/folders/mn/7jdnkyy91_n0bxkrgjgjk17c0000gn/T/d20191212-36568-33nxni'...
error: RPC failed; curl 56 LibreSSL SSL_read: SSL_ERROR_SYSCALL, errno 54
fatal: the remote end hung up unexpectedly
fatal: early EOF
fatal: unpack-objects failed
```

#### 报错原因：

`RPC(Remote Procedure Call)`连接失败，基本可以判定是由于网络原因，这种可能性很多，代理、局域网等等，尤其是针对国外的一些资源，对于国内的开发者来说，很不友好。

## 环境

```
info Fetching system and libraries information...
System:
    OS: macOS Mojave 10.14.5
    CPU: (4) x64 Intel(R) Core(TM) i3-8100B CPU @ 3.60GHz
    Memory: 221.88 MB / 8.00 GB
    Shell: 5.3 - /bin/zsh
  Binaries:
    Node: 12.10.0 - /usr/local/bin/node
    Yarn: 1.17.3 - ~/.yarn/bin/yarn
    npm: 6.11.3 - /usr/local/bin/npm
    Watchman: 4.9.0 - /usr/local/bin/watchman
  SDKs:
    iOS SDK:
      Platforms: iOS 12.2, macOS 10.14, tvOS 12.2, watchOS 5.2
    Android SDK:
      API Levels: 23, 26, 27, 28
      Build Tools: 23.0.1, 26.0.1, 27.0.3, 28.0.2, 28.0.3
      System Images: android-23 | Intel x86 Atom_64, android-23 | Google APIs Intel x86 Atom, android-23 | Google APIs Intel x86 Atom_64, android-26 | Google APIs Intel x86 Atom, android-27 | Intel x86 Atom_64, android-27 | Google APIs Intel x86 Atom, android-27 | Google Play Intel x86 Atom, android-28 | Google Play Intel x86 Atom
  IDEs:
    Android Studio: 3.5 AI-191.8026.42.35.5977832
    Xcode: 10.2.1/10E1001 - /usr/bin/xcodebuild
  npmPackages:
    react: 16.9.0 => 16.9.0
    react-native: 0.61.5 => 0.61.5
  npmGlobalPackages:
    react-native-cli: 2.0.1
    react-native-update-cli: 0.1.0
```

## 重现

我们就基于现有的环境新建一个项目来重现这个`RPC`错误。

#### 创建

使用`CLI`新建一个`RN`项目，运行如下命令：

```
react-native init demo
```

> 在这里，我使用的是默认新建最新版本，目前`RN`的最新版本是 `0.61.5`，你也可以使用`react-native init 工程名 --version 版本`指定某一个具体的`RN`版本，比如要创建一个`0.59.9`的版本，可以使用`react-native init demo --version 0.59.9`。

#### 安装依赖

进入到该`/demo/ios`目录下，运行如下命令：

```
pod install
```

> 注意：在`RN`版本大于`0.6`的情况下，`iOS`工程默认使用`cocoapods`来集成项目环境，因此，在运行项目之前，必须先使用`cocoapods`安装所需依赖，因此要运行`pod install`命令。对于`0.6`以下的`RN`项目，也可以进入到`/ios`目录下运行`pod init`生成一个`Podfile`文件，在`Podfile`文件里面添加依赖，然后运行`pod install`安装依赖，原理是一样的。

#### 错误产生❌

在运行`pod install`后，等待了大概十几分钟（特别慢），报错如下：

```
➜  ios pod install
Analyzing dependencies
Fetching podspec for `DoubleConversion` from `../node_modules/react-native/third-party-podspecs/DoubleConversion.podspec`
Fetching podspec for `Folly` from `../node_modules/react-native/third-party-podspecs/Folly.podspec`
Fetching podspec for `glog` from `../node_modules/react-native/third-party-podspecs/glog.podspec`
Downloading dependencies
Installing DoubleConversion (1.1.6)

[!] Error installing DoubleConversion
[!] /usr/local/bin/git clone https://github.com/google/double-conversion.git /var/folders/mn/7jdnkyy91_n0bxkrgjgjk17c0000gn/T/d20191212-36568-33nxni --template= --single-branch --depth 1 --branch v1.1.6

Cloning into '/var/folders/mn/7jdnkyy91_n0bxkrgjgjk17c0000gn/T/d20191212-36568-33nxni'...
error: RPC failed; curl 56 LibreSSL SSL_read: SSL_ERROR_SYSCALL, errno 54
fatal: the remote end hung up unexpectedly
fatal: early EOF
fatal: unpack-objects failed
```

## 解决

为了能够尽可能安装成功，在自动安装失败的情况下，我们采用手动安装来解决这个问题。

首先，进入[Github: react-native官网的目录react-native/scripts/ios-install-third-party.sh下](https://github.com/facebook/react-native/blob/master/scripts/ios-install-third-party.sh)，你将能够看到：

![shot.png](https://i.loli.net/2019/12/13/sqOlS5ENi4oAxtZ.png)

> 注意：左边的`Branch`选择是对应你当前是所用的`RN`版本，比如你使用的是`0.61.5`，那对应的就是上图中的`0.61-stable`，如果你使用的是`0.59.9`，那对应的就是`0.59-stable`。但是，一般情况下，有部分连续的版本的`ios-install-third-party.sh`对应的编译库是一样的，这个可以自己去查看。为了保险起见，尽量保持和你当前所使用的`RN`版本一致。

进入到`ios-install-third-party.sh`文件后，滑到最底部，你将会看到如下图所示：

![shot.png](https://i.loli.net/2019/12/13/74QEHstVo6krmKx.png)

> 注意：我们可以看到有`4`个对应的编译库文件就是相应的`RN`版本所需要的编译库，这`4`个编译库都需要你下载。

接下来，我们有`2`种方式来下载`4`个编译库对应的压缩包：

- 第一种方式

[依赖库下载链接：RN中文网作者的第三方依赖库百度网盘链接](https://pan.baidu.com/s/1kVDUAZ9#list/path=%2F)

- 第二种方式

如果在`第一种方式`中没有找到对应的依赖库版本（有可能随着版本更新，`RN`中文网的作者没来得及更新或者忘记了），那么你可以用另一种稍微麻烦一点但是`100%`正确的方式来下载你所需要的依赖库，继续往下看

在上面的图片中，我们看到了`4`个依赖库以及对应的链接，是的，没错，我们可以通过对应的链接找到相应的压缩包版本，然后下载。这里以第一个`glog-0.3.5.tar.gz`为例，它对应所在的`github`链接地址是：`https://github.com/google/glog`（后面的一串串不需要，我们只需要它所在的`github`地址），打开之后如下：

![shot.png](https://i.loli.net/2019/12/13/oaRvN3xygQY1TAl.png)

点击上图中的`releases`，进入可以找到相应版本如下：

![shot.png](https://i.loli.net/2019/12/13/Zs9IC3dUPno6TRG.png)

然后点击红圈中的`Source code(tar.gz)`下载，完成。（其他`3`个依赖库类似）

下载完`4`个依赖压缩包之后，不用解压，我们直接放到相应的目录当中：

- `RN`版本在`>=0.58`

将压缩包放到 `~/Library/Caches/com.facebook.ReactNativeBuild`

```
➜  ~ cd ~/Library/Caches/com.facebook.ReactNativeBuild
➜  com.facebook.ReactNativeBuild pwd
/Users/langke/Library/Caches/com.facebook.ReactNativeBuild
➜  com.facebook.ReactNativeBuild ls
boost_1_63_0.tar.gz  double-conversion-1.1.6.tar.gz folly-2018.10.22.00.tar.gz  glog-0.3.5.tar.gz
```

> 注意：或许在`~/Library/Caches`目录下，你并没有`com.facebook.ReactNativeBuild`文件夹，怎么办？进入到`cd ~/Library/Caches`目录下，然后使用`mkdir com.facebook.ReactNativeBuild`创建一个就好了。

- `RN`版本在`<0.58`

将压缩包放到 `~/.rncache`目录下，一般情况下`.rncache`文件夹也是有的，如果没有，同理，使用 `mkdir .rncache`创建一个就好。

> 注意：虽然我们可以使用环境变量的方式自定义路径，但是我个人还是建议按照正常的这种全局方式去配置比较好，毕竟，自定义路径很可能因为个人疏忽而出错，这个也因人而异，如果想要使用具体环境变量自定义路径，可前往：[RN中文网作者的指导说明](https://www.reactnative.cn/topic/4301/ios-rn-0-45%E4%BB%A5%E4%B8%8A%E7%89%88%E6%9C%AC%E6%89%80%E9%9C%80%E7%9A%84%E7%AC%AC%E4%B8%89%E6%96%B9%E7%BC%96%E8%AF%91%E5%BA%93-boost%E7%AD%89)，本文也主要来源于此的参考。

## 完毕

运行 `pod install`: 

![shot.png](https://i.loli.net/2019/12/13/JyQdX1U8mTx5CiV.png)

经过上面的一番小折腾，到此为止，基本告一段落，也应该可以稍微正常`pod install`了。还是会有点慢，甚至也有可能会失败，但是多尝试几次应该会成功。






