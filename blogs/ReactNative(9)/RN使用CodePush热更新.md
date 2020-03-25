---
title: react-native-code-push热更新
date: 2020-03-25 15:41:33
categories: React Native
tags: React Native, react-native-code-push, 热更新
---

## 前期准备

[官方链接](https://docs.microsoft.com/en-us/appcenter/distribution/codepush/)

## 环境

本文测试都是基于以下配置进行测试

```
info
  React Native Environment Info:
    System:
      OS: macOS 10.14.5
      CPU: (4) x64 Intel(R) Core(TM) i3-8100B CPU @ 3.60GHz
      Memory: 192.60 MB / 8.00 GB
      Shell: 5.3 - /bin/zsh
    Binaries:
      Node: 12.10.0 - /usr/local/bin/node
      Yarn: 1.17.3 - ~/.yarn/bin/yarn
      npm: 6.11.3 - /usr/local/bin/npm
      Watchman: 4.9.0 - /usr/local/bin/watchman
    SDKs:
      iOS SDK:
        Platforms: iOS 13.2, DriverKit 19.0, macOS 10.15, tvOS 13.2, watchOS 6.1
      Android SDK:
        API Levels: 23, 26, 27, 28
        Build Tools: 23.0.1, 26.0.1, 27.0.3, 28.0.2, 28.0.3
        System Images: android-23 | Intel x86 Atom_64, android-23 | Google APIs Intel x86 Atom, android-23 | Google APIs Intel x86 Atom_64, android-26 | Google APIs Intel x86 Atom, android-27 | Intel x86 Atom_64, android-27 | Google APIs Intel x86 Atom, android-27 | Google Play Intel x86 Atom, android-28 | Google Play Intel x86 Atom
    IDEs:
      Android Studio: 3.5 AI-191.8026.42.35.5977832
      Xcode: 11.3.1/11C505 - /usr/bin/xcodebuild
    npmPackages:
      react: 16.8.3 => 16.8.3
      react-native: 0.59.9 => 0.59.9
    npmGlobalPackages:
      react-native-cli: 2.0.1
      react-native-demo-for-npm: 1.0.16
      react-native-update-cli: 0.1.0
```

## 过程

### 1. Install the App Center CLI

要管理大多数**CodePush**功能，需要使用**App Center CLI**。通过以下命令安装

```
npm install -g appcenter-cli
```

> 注意：官方提示，不要使用`sudo`权限来执行安装这个命令。

在成功安装**App Center CLI**后，执行下面的命令来配置你的**App Center**账户详情

```
appcenter login
```

账户信息：

```
Logged in as wankeenergy-gmail.com
```

##### 1.1 在App Center上创建App

- iOS

```
appcenter apps create -d PowerPlus-iOS -o iOS -p React-Native
```

创建成功输出类似如下

```
➜  SolarEnergy git:(dev_chenlong) ✗ appcenter apps create -d PowerPlus-iOS -o iOS -p React-Native
App Secret:            28a33816-6bc0-4b00-8947-97b28e7780e4
Description:
Display Name:          PowerPlus-iOS
Name:                  PowerPlus-iOS
OS:                    iOS
Platform:              React-Native
Release Type:
Owner ID:              20273273-6efd-459d-bb13-5cf102ba28d4
Owner Display Name:    Energy Wanke
Owner Email:           wankeenergy@gmail.com
Owner Name:            wankeenergy-gmail.com
Azure Subscription ID:
```

- Android

```
appcenter apps create -d PowerPlus-Android -o Android -p React-Native
```

我们可以把**App Center**上的应用程序`PowerPlus-iOS`设置为当前的应用程序

```
appcenter apps set-current wankeenergy-gmail.com/PowerPlus-iOS
```

设置测试（`Staging`）)和 生产（`Production`）环境密钥，这样的话避免测试的时候应用到生产环境

```
appcenter codepush deployment add -a <ownerName>/<appName> Staging
appcenter codepush deployment add -a <ownerName>/<appName> Production
```

由于我们刚才使用`appcenter apps set-current`设置了`PowerPlus-iOS`设置为当前的应用程序, 所以直接

```
appcenter codepush deployment add Staging
```

输出如下

```
➜  SolarEnergy git:(dev_chenlong) ✗ appcenter codepush deployment add Staging
Deployment Staging has been created for wankeenergy-gmail.com/PowerPlus-iOS with key _aoveTNJlRogvD_AZL16sr1dYl-JIG-moOdTY
```

部署生产环境

```
➜  SolarEnergy git:(dev_chenlong) ✗ appcenter codepush deployment add Production
Deployment Production has been created for wankeenergy-gmail.com/PowerPlus-iOS with key ZqjsB9UA9BX5KylIOgNU4lCkSlDwCRVZudOoH
```

将当前应用程序设置成`Android`

```
appcenter apps set-current wankeenergy-gmail.com/PowerPlus-Android
```

```
➜  SolarEnergy git:(dev_chenlong) ✗ appcenter codepush deployment add Staging
Deployment Staging has been created for wankeenergy-gmail.com/PowerPlus-Android with key tFRH5R1fNGA_BTQ_P4ixCiR4v6TsEUL4iSMZj

➜  SolarEnergy git:(dev_chenlong) ✗ appcenter codepush deployment add Production
Deployment Production has been created for wankeenergy-gmail.com/PowerPlus-Android with key Daj3dgNAeyKYFzAqWGWfoV9PggYm8eX76vbMC
```

可以使用下面的命令来查看当前应用程序的部署`key`

```
➜  SolarEnergy git:(dev_chenlong) ✗ appcenter codepush deployment list --displayKeys
```

输出如下

Name | Key 
---|---
Staging| tFRH5R1fNGA_BTQ_P4ixCiR4v6TsEUL4iSMZj
Production| Daj3dgNAeyKYFzAqWGWfoV9PggYm8eX76vbMC

如果想改变已命名的应用程序名，可以使用如下命令

```
appcenter apps update -n <newName> -a <ownerName>/<appName>
```

要想从服务器上移除`app`，可以使用如下命令(要注意，如果删除，则无法再更新`app`）

```
appcenter apps delete -a <ownerName>/<appName>
```

如果想要列举所有在**App Center**服务器上的`app`，可以运行如下命令

```
appcenter apps list
```

比如

```
➜  SolarEnergy git:(dev_chenlong) ✗ appcenter apps list
* wankeenergy-gmail.com/PowerPlus-Android (current app)
  wankeenergy-gmail.com/PowerPlus-iOS
```

回滚版本

```
appcenter codepush rollback
```

### 2. CodePush-ify你的app

添加**CodePush client SDK**到你的`app`，通过配置，从**App Center**中拉取所部署的`app`更新, 详情可参考

- [React Native](https://docs.microsoft.com/en-us/appcenter/distribution/codepush/react-native#getting-started)

##### 2.1 开始

```
npm install --save react-native-code-push@5.7.0
```

`Android`和`iOS`的配置方式有所不同，要注意的是，官方推荐为每一个平台单独创建一个**CodePush**应用程序。如果你想要看一下其他工程如何集成**CodePush**，可以看一下社区提供的一些样例`app`: [example apps](https://docs.microsoft.com/en-us/appcenter/distribution/codepush/react-native#example-apps--starters)。此外，如果你想快速熟悉**CodePush + React Native**，可以看一下这些人制作的视频：[Bilal Budhani](https://www.youtube.com/watch?v=uN0FRWk-YW8&feature=youtu.be)和[Deepak Sisodiya](https://www.youtube.com/watch?v=f6I9y7V-Ibk)。

##### 2.2 iOS设置（这里按RN版本小于0.6来安装，因为我是0.59.9）

```
react-native link react-native-code-push
```

上面这个命令会提示输入一个部署`key`(这个`key`就是上面已经添加的`Staging`和`Production`)，可以按回车键忽略，后面再配置也可以，可以通过以下命令来获取部署`key`

```
appcenter codepush deployment list -a <ownerName>/<appName> --displayKeys
```

##### 2.3 到这里之后，我们运行`react-native run-android`可能会报错如下：

```
* What went wrong:
Execution failed for task ':app:transformDexArchiveWithDexMergerForDebug'.
> com.android.build.api.transform.TransformException: java.lang.RuntimeException: java.lang.RuntimeException: com.android.builder.dexing.DexArchiveMergerException: Error while merging dex archives:
  The number of method references in a .dex file cannot exceed 64K.
  Learn how to resolve this issue at https://developer.android.com/tools/building/multidex.html
```

解决办法，在`android/app/build.gradle`下添加

```
android {

	...
	
	defaultConfig {
	
	...
	
	   // Enabling multidex support.
        multiDexEnabled true
	}
	
	...
}
```


##### 2.4 在项目根组件添加CodePush

```
import codePush from "react-native-code-push";

class MyApp extends Component {

	   componentDidMount() {
	   		  /* 
	   		    注意：如果我们分别在Android和iOS中配置了 Staging 和 Production 密钥，那么我们这里可以
	   		         不需要deploymentKey！！！当然，要传也没有关系，传的话就是代表动态使用这个 deploymentKey，会覆
	   		         盖在工程中配置的key，看个人的选择！
	   		   */
        	  const deploymentKey = isAndroid ? 'tFRH5R1fNGA_BTQ_P4ixCiR4v6TsEUL4iSMZj' : '_aoveTNJlRogvD_AZL16sr1dYl-JIG-moOdTY';
        	  codePush.sync({
                   updateDialog: false,
                   installMode: codePush.InstallMode.ON_NEXT_RESTART,
                   deploymentKey: deploymentKey,
       	       }, status => {
           		 	__DEV__ && console.warn(`status: ${status}`)
       	 	   }, progress => {
           		 	const {totalBytes, receivedBytes} = progress;
            		__DEV__ && console.warn(`totalBytes: ${totalBytes}, receivedBytes: ${receivedBytes}`);
        	   });
       }

}

MyApp = codePush(MyApp);
```


### 3. 部署测试和生产环境密钥进行多端测试

[Multi-Deployment Testing](https://docs.microsoft.com/en-us/appcenter/distribution/codepush/react-native#multi-deployment-testing)

### 4. 发布app更新

注意：`Android`打包测试热更新的时候用下面这个命令

```
./gradlew assembleReleaseStaging
```

如果用`./gradlew assembleRelease`的话，默认走的是`Production`。

如果需要直接在本地调试安装在`android`手机上的`Staging`版本，可以执行

```
react-native run-android --variant=releaseStaging
```

`iOS`打包需要在`archive`的时候选择`Staging`，否则打的也是`Production`。


在更新代码后，使用**App Center CLI**发布更新到**App Center**，根据下面发布即可

##### React Native

执行**App Center CLI**的`release-react`命令来打包代码和`asset`文件，然后发布到**App Center server**作为一个新的发布。例如

```
appcenter codepush release-react -a <ownerName>/MyApp
```

> **App Center CLI**的一个重要特性就是可以使用`appcenter apps set-current <ownerName>/<appName>`把一个`app`设置为当前的`app`. 通过把`app`设置为当前`app`，就没有必要使用`-a`标志。例如，命令`appcenter codepush deployment list -a <ownerName>/<appName>`可以用`appcenter codepush deployment list`来代替。你也可以使用命令`appcenter apps get-current`来查看哪个`app`被设置成你账号的当前`app`。

发布一个更新(加`-m`就是强制更新，会立即重启`app`)，并带有日志描述

```
// 强制立即重启
appcenter codepush release-react -a <ownerName>/MyApp-iOS  -m --description "Modified the header color"

// 不强制立即重启
appcenter codepush release-react -a <ownerName>/MyApp-iOS  --description "Modified the header color"
```

发布更新到具体的版本

```
appcenter codepush release-react -a <ownerName>/MyApp-Android  --target-binary-version "1.1.0"
```

也可以指定范围要发布到哪些版本，参数如下

```
Range Expression	Who gets the update
1.2.3				Only devices running the specific binary version 1.2.3 of your app
*	    			Any device configured to consume updates from your CodePush app
1.2.x				Devices running major version 1, minor version 2, and any patch version of your app
1.2.3 - 1.2.7		Devices running any binary version between 1.2.3 (inclusive) and 1.2.7 (inclusive)
>=1.2.3 <1.2.7		Devices running any binary version between 1.2.3 (inclusive) and 1.2.7 (exclusive)
1.2	    			Equivalent to >=1.2.0 <1.3.0
~1.2.3				Equivalent to >=1.2.3 <1.3.0
^1.2.3				Equivalent to >=1.2.3 <2.0.0
```

要发布到生产环境还是测试环境，可以使用`--deployment-name 或 -d`，默认是`Staging`, 例如要发布到版本`1.1.0`的生产环境

```
appcenter codepush release-react -a <ownerName>/MyApp-Android  --target-binary-version "1.1.0" --deployment-name Production
```

### 5. 运行你的app

一旦这些步骤完成，所有运行`app`的用户都会收到更新。