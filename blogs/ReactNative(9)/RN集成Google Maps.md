---
title: RN集成Google Maps
date: 2020-03-14 10:00:01
categories: React Native
tags: React Native, Google Maps, 谷歌地图
---

## 环境

```
  React Native Environment Info:
    System:
      OS: macOS 10.14.5
      CPU: (4) x64 Intel(R) Core(TM) i3-8100B CPU @ 3.60GHz
      Memory: 53.88 MB / 8.00 GB
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
      react: 16.8.3 => 16.8.3
      react-native: 0.59.9 => 0.59.9
    npmGlobalPackages:
      react-native-cli: 2.0.1
      react-native-demo-for-npm: 1.0.16
      react-native-update-cli: 0.1.0
```

## Android

- 1. 安装

```
npm install react-native-maps@0.25.0
```

- 2. 在android/settings.gradle中添加如下内容：

```
rootProject.name = 'AndroidGoogleMaps' 
include ':react-native-maps' 
project(':react-native-maps').projectDir = new File(rootProject.projectDir, '../node_modules/react-native-maps/lib/android')
include ':app'
```

- 3. 在`android/app/build.gradle`添加：

```
dependencies {
    implementation fileTree(dir: "libs", include: ["*.jar"])
    implementation "com.android.support:appcompat-v7:${rootProject.ext.supportLibVersion}"
    implementation "com.facebook.react:react-native:+"  // From node_modules
    implementation project(':react-native-maps') // 添加这行
}
```

- 4. 在`android/build.gradle`添加：

```
 ext {
        buildToolsVersion = "28.0.3"
        minSdkVersion = 16
        compileSdkVersion = 28
        targetSdkVersion = 28
        supportLibVersion = "28.0.0"
        playServicesVersion = "16.0.0"  // 添加这行
        androidMapsUtilsVersion="0.5+"  // 添加这行
    }
```

- 5. 在`android/app/src/main/AndroidManifest.xml`添加

```
<application
      .......
	<meta-data android:name="com.google.android.geo.API_KEY" android:value="AIzaSyAZ7eaTLpbEmz7xWGst_-pSXYcv9SU6D_u"/>
	<uses-library android:name="org.apache.http.legacy" android:required="false"/>
</application>
```

> 添加 `<uses-library android:name="org.apache.http.legacy" android:required="false"/> `的原因是： Android翻墙后，会奔溃：[https://github.com/react-native-community/react-native-maps/issues/2991#issuecomment-516765727](https://github.com/react-native-community/react-native-maps/issues/2991#issuecomment-516765727)。

- 6. 在`MainApplication.java`中添加

```
import com.airbnb.android.react.maps.MapsPackage;

    @Override
    protected List<ReactPackage> getPackages() {
      return Arrays.<ReactPackage>asList(
          new MainReactPackage(),
          new MapsPackage()  // 添加这行
      );
    }
```

- 7. 在App.js文件中添加以下内容：

```
import MapView from 'react-native-maps';
import React, {Component} from 'react';
import {
  StyleSheet,
  Dimensions,
} from 'react-native';

const styles = StyleSheet.create({
  container: {
    position: 'absolute',
    top: 0,
    left: 0,
    right: 0,
    bottom: 0,
    justifyContent: 'flex-end',
    alignItems: 'center',
  },
  map: {
    // ...StyleSheet.absoluteFillObject
    position: 'absolute',
    top: 0,
    left: 0,
    right: 0,
    bottom: 0,
    //   width: Dimensions.get('window').width,
    //   height: 500,
  },
});

/**
 * 这个工程，只集成、测试过Android， 并且必须翻墙，一些配置需要参考这里面的。
 */

class App extends Component {

  render() {
    return (
        <MapView
            style={styles.map}
            initialRegion={{
              latitude: 30.2,
              longitude: 120.2,
              latitudeDelta: 0.0922,
              longitudeDelta: 0.0421,
            }}
        />
    );
  }

}

export default App;

```

## iOS

- 1. 链接

```
react-native link react-native-maps
```

- 2. 在`AppDelegate.m`中添加

```
#import <GoogleMaps/GoogleMaps.h>
[GMSServices provideAPIKey:@"AIzaSyAZ7eaTLpbEmz7xWGst_-pSXYcv9SU6D_u"];
```

- 3. 手动集成`Google Maps SDK`

[Google Maps Platform iOS SDK Manually](https://developers.google.com/maps/documentation/ios-sdk/start)

1.下载SDK（这里下载的是GoogleMaps-3.7.0）

2.解包SDK

3.打开xcode

4.将下面解压的文件拖到工程里面

```
GoogleMaps-x.x.x/Base/Frameworks/GoogleMapsBase.framework
GoogleMaps-x.x.x/Maps/Frameworks/GoogleMaps.framework
GoogleMaps-x.x.x/Maps/Frameworks/GoogleMapsCore.framework
```

5.右键`GoogleMaps.framework` -> `Show In Finder`

6.将`Resources/GoogleMaps.bundle`拖入工程，并且勾选`Create Folder References`

7.在`Build Phases/Link Binary With Libraries`中添加以下内容

```
GoogleMapsBase.framework
GoogleMaps.framework
GoogleMapsCore.framework
Accelerate.framework
CoreData.framework
CoreGraphics.framework
CoreImage.framework
CoreLocation.framework
CoreTelephony.framework
CoreText.framework
GLKit.framework
ImageIO.framework
libc++.tbd
libz.tbd
OpenGLES.framework
QuartzCore.framework
SystemConfiguration.framework
UIKit.framework
```

8.在`Info.plist`中添加隐私定位描述

9.在`package.json`中添加

```
"scripts": {
    "start": "node node_modules/react-native/local-cli/cli.js start",
    "postinstall": "./node_modules/react-native-maps/enable-google-maps './ios/**'",
    "test": "jest"
  }
  
```
 
10.执行

```
npm install
```
 
经过以上步骤，基本就能在`Android`和`iOS`端显示谷歌地图了。要注意的是，需要申请相应的`key`，需要到谷歌云平台申请，并且现在谷歌要求信用卡验证账号才可以使用，部分`API`需要收费，但是开始会有一定的免费额度，总的来说很多`API`比较贵。
 
