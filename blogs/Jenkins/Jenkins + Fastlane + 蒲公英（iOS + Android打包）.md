# Jenkins + Fastlane + 蒲公英（iOS + Android打包）

## 一 前言

本教程结合`Jenkins`、`Fastlane`和`蒲公英`对`iOS`和`Android`实现自动化打包并上传蒲公英，详细说明了打包的`jenkins配置`、`fastlane脚本配置`并上传到蒲公英。然而，针对安装`jenkins`并没有详细的大量篇幅说明，若安装`jenkins`出现问题，可自行`Google`或`去官方网站`查询，资料很多，这里并不一一列举。

## 二 环境

本文主要针对`React Native`项目对`iOS`和`Android`进行打包，笔者也亲自实践过原生项目的打包，除了路径不一样，其余都是一模一样的。

环境如下：

```
React Native Environment Info:
    System:
      OS: macOS 10.14.5
      CPU: (4) x64 Intel(R) Core(TM) i3-8100B CPU @ 3.60GHz
      Memory: 166.11 MB / 8.00 GB
      Shell: 5.3 - /bin/zsh
    Binaries:
      Node: 11.5.0 - /usr/local/bin/node
      Yarn: 1.17.3 - ~/.yarn/bin/yarn
      npm: 6.4.1 - /usr/local/bin/npm
      Watchman: 4.9.0 - /usr/local/bin/watchman
    SDKs:
      iOS SDK:
        Platforms: iOS 12.2, macOS 10.14, tvOS 12.2, watchOS 5.2
      Android SDK:
        API Levels: 23, 26, 27, 28
        Build Tools: 23.0.1, 26.0.1, 27.0.3, 28.0.2, 28.0.3
        System Images: android-23 | Intel x86 Atom_64, android-23 | Google APIs Intel x86 Atom, android-23 | Google APIs Intel x86 Atom_64, android-27 | Intel x86 Atom_64, android-27 | Google APIs Intel x86 Atom, android-27 | Google Play Intel x86 Atom
    IDEs:
      Android Studio: 3.3 AI-182.5107.16.33.5314842
      Xcode: 10.2.1/10E1001 - /usr/bin/xcodebuild
    npmPackages:
      react: ^16.6.0-alpha.8af6728 => 16.6.0-alpha.8af6728
      react-native: 0.57.4 => 0.57.4
    npmGlobalPackages:
      react-native-cli: 2.0.1
      react-native-update-cli: 0.1.0
```

## 三 安装

#### 3.1 安装`Jenkins`

- 3.1.1 升级ruby

> 由于`fastlane`依赖`ruby环境`，默认情况下，`Mac`下得`ruby`或者版本比较低，这取决于你的系统，但是建议对其进行升级到最新版本，以免后续出错。

*3.1.1.1 查看当前`ruby`版本：*

```
ruby -v
```

*3.1.1.2 安装`rvm`(Ruby Version Manager)*

**注：**[fastlane官方建议](https://docs.fastlane.tools/best-practices/continuous-integration/jenkins/)使用[rbenv](https://github.com/rbenv/rbenv)来安装和管理`ruby`。事实上，使用`rvm`已经足够，取决于个人的选择。

```
\curl -sSL https://get.rvm.io | bash -s stable --ruby
```

*3.1.1.3 退出终端，重新打开，查看是否安装成功*

```
rvm -v
```

*3.1.1.4 列举出ruby所有版本* 

```
rvm list known
```

*3.1.1.5 升级ruby*

```
rvm install 版本号 // 注意：这个版本号是你刚才列举出来的ruby所有版本号之一，一般选择最新的即可
```


- 3.1.2 安装`Jenkins`

```
brew update && brew install jenkins
```

若遇到问题，请查看[Jenkins官网](https://jenkins.io/zh/doc/book/installing/) 或 自行`Google`。

> 注：不推荐直接下载`pkg`包直接安装。

#### 3.2 安装`xcode`打包所需命令行工具

```
xcode-select --install
```

#### 3.3 安装`fastlane`

- 方式一，使用`Homebrew`安装


```
brew cask install fastlane
```

- 方式二， 使用`RubyGems`安装

```
gem install fastlane -NV
```

> 对于方式二，若遇到无执行权限，可尝试：`sudo gem install fastlane -NV`。

#### 3.4 设置环境变量

将一下命令添加到你的`~/.bashrc`, `~/.bash_profile`, `~/.profile` 或者 `~/.zshrc`中：

```
export LC_ALL=en_US.UTF-8
export LANG=en_US.UTF-8
```

> 注：添加的目的是保证`fastlane`能够在`utf-8`编码环境下执行，否则在未设置`utf-8`为默认编码的系统上，`fastlane`运行可能会出现错误❌。

接着使其生效：

```
source ~/.bashrc
```

> 注：如果你是添加到`~/.bashrc`中，就使用`source ~/.bashrc`; 若你是添加到`~/.bash_profile`中，就使用`source ~/.bash_profile`，以此类推。

## 四 初始化

#### 4.1 使用`Gemfile`(可选，但是建议安装)

> 注：这只是`fastlane`官方推荐，原因如果我们使用`Gemfile`文件的话，我们可以使用它来定义`fastlane`的相关依赖，其次这也可以让我们现在所使用到的`fastlane`版本一目了然，并且还可以加快`fastlane`命令的执行，这**不是必须的步骤！！！**

- 4.1.1 在工程根目录下（xxx.xcodeproj所在目录）创建`Gemfile`

> （一般是根目录，如果是`React Native`项目，则在`ios`目录下， `Android`的话进入`.../android`目录下， `Android`没有`scheme.xcodeproj`）

```
touch Gemfile
```

- 4.1.2 将以下内容添加到刚才创建的`Gemfile`文件中

```
source "https://rubygems.org"

gem "fastlane"
```

- 4.1.3 安装`bundle`命令

请依次执行以下命令：

```
bundle install

bundle update
```

> 注1：如果以上执行没有权限，请在前面添加 `sudo` 命令。以上会生成 `Gemfile` 和 `Gemfile.lock` 文件，可以提交到版本控制系统`git`中。

> 注2：后面执行`fastlane`命令的时候，我们就可以使用 `bundle exec fastlane [lane]`， 后面的 `[lane]` 代表你在`Fastfile`文件里面配置的`lane项`，比如：`bundle exec fastlane topgyer`命令中的`toppgyer`。

> 注3: 如果要更新`fastlane`，也可以使用: `[sudo] bundle update fastlane`命令。

#### 4.2 初始化`fastlane`

> `cd`到 `scheme.xcodeproj`所在目录（一般是根目录，如果是`React Native`项目，则在`ios`目录下， `Android`的话进入`.../android`目录下， `Android`没有`scheme.xcodeproj`）

```
fastlane init
```

出现：`What would you like to use fastlane for?`

答：4

> 根据提示选择相应的提示项，一路往下走即可。

## 五 添加插件

这里我们添加的是`蒲公英`插件：


```
fastlane add_plugin pgyer
```

## 六 修改配置

#### 6.1 iOS

`cd`到`ios/fastlane`目录下，修改`Fastfile`文件，替换所有内容为：

```
# 打包平台
default_platform(:ios)

platform :ios do
desc "上传ad-hoc的ipa包到蒲公英平台" 

  lane :UploadToPgyer do|option|
    # 自动增加Build
    increment_build_number
    
    # ❣️编译的scheme，也就是 “xxx.xcodeproj” 中的 “xxx”(必须一致！！！)
    scheme_name = "工程名"

    # 获取项目中的 Version 和 Build
    version = get_info_plist_value(path: "./#{scheme_name}/Info.plist", key: "CFBundleShortVersionString")
    build = get_info_plist_value(path: "./#{scheme_name}/Info.plist", key: "CFBundleVersion")
    
    # 导出ipa包路径（这里放在当前所在的build目录下，打包成功后，ipa包就可以在这里找到哦）
    output_directory = "./build"
    
    # 导出ipa的名称：例如（wanke_1.0.0_5_20190910111549.ipa），也可以写死，这是灵活的，取决于你的需要。
    # ① wanke：这是你的工程名；1.0.0：这是你的版本号（取决于你在xcode中的设置）
    # ② 20190910111549：这是详细时间，形式为：年-月-日-小时-分钟-秒。代表的是你本次打包的时间。
    output_name = "#{scheme_name}_#{version}_#{build}_#{Time.now.strftime('%Y%m%d%H%M%S')}.ipa"

    # 打包时输出提示，可随便修改，或者不要都行
    puts "-----开始打包-----"
    
    gym(
      export_method: "ad-hoc",  # 导出方式：ad-hoc（测试包）、app-store（上传到App Store包）、enterprise（企业测试包）
      export_xcargs: "-allowProvisioningUpdates",
      export_options:{
         provisioningProfiles: {
            "cn.com.kkk" => "iPhone Distribution: kkk. (77777XXRRR)"
         }
      },
      scheme: scheme_name,
      clean: true, # 每次打包都清空上一次的编译信息
      output_directory: output_directory,
      output_name: output_name
    )
    
    # 打包时输出提示，可随便修改，或者不要都行
    puts "-----开始上传到蒲公英平台-----"
    
    # ❣️蒲公英平台的 API Key 和 User Key（直接复制过来即可）
    pgyer(api_key: "蒲公英API Key", user_key: "蒲公英User Key")
  end

end

```

> iOS注意：`Fastfile`文件中加❣️的地方为必填项，其他地方基本不用改动即可直接打包，如果想自己改动或添加其他参数也可以，自行`Google`或去官方查看命令！！！

#### 6.2 Android

`cd`到`anddroid/fastlane`目录下，修改`Fastfile`文件，替换所有内容为：

```
default_platform(:android)

platform :android do
  desc "上传android的apk包到蒲公英平台"
  lane :UploadToPgyer do
     gradle(task: 'clean')
     gradle(task: "assembleRelease")
    # ❣️蒲公英平台的 API Key 和 User Key（直接复制过来即可）
    pgyer(api_key: "蒲公英API Key", user_key: "蒲公英User Key")
  end
end
```

> Android注意：`Fastfile`文件中加❣️的地方为必填项，其他地方基本不用改动即可直接打包，如果想自己改动或添加其他参数也可以，自行`Google`或去官方查看命令！！！

## 七 打包上传

#### 7.1 本地直接打包

> （一般是根目录，如果是`React Native`项目，则在`ios`目录下， `Android`的话进入`.../android`目录下， `Android`没有`scheme.xcodeproj`）

```
fastlane UploadToPgyer
```

#### 7.2 使用`Jenkins`打包

> 说明：在使用`Jenkins`打包之前，请先尝试`7.1`能够在本地打包成功，因为`Jenkins`只是一个工具，最终执行还是本地`fastfile`文件中的脚本，若本地打包失败，意味着你在`jenkins`上配置出来打包必然失败。

`jenkins`安装好后，配置以及创建项目这里就直接忽略，有问题自行`Google`。接下来直接进入配置项。

- **7.2.1 `general`**

![1241568256881_.pic_hd.jpg](https://i.loli.net/2019/09/12/d6vytZ87IHOgPVw.png)
     
- **7.2.2 源码管理**

![496131568186987_.pic_hd.jpg](https://i.loli.net/2019/09/11/C6JfdemONPYoRHA.png)

- **7.2.3 构建触发器（不需要，可跳过）**

- **7.2.4 构建环境**

![496141568187214_.pic.jpg](https://i.loli.net/2019/09/11/eKa54XmDkqhGrxM.png)

- **7.2.5 构建**

![1251568258617_.pic_hd.jpg](https://i.loli.net/2019/09/12/28EUzOMVWySrqQu.png)

`shell脚本`如下（可直接复制粘贴使用，需要修改的地方以下有说明）：

```
# 使环境变量配置生效
source ~/.bash_profile
case $Platform in
    iOS)
         echo "❣️❣️❣️开始iOS打包，成功后自动上传到蒲公英"
         # 解锁Mac上的钥匙串（123456789是你自己的开机密码），LiuDeHua是你自己的用户名，可用pwd命令查看
		 security unlock-keychain -p 123456789 /Users/LiuDeHua/Library/Keychains/login.keychain
		 # 这个目录就是jenkins拉取的工程所在的目录，只有这个目录下可以打包
		 cd /Users/LiuDeHua/jenkins/workspace/CIProject/JenkinsDemo/ios
		 # 这里主要是为了移除上一次打的ipa包，留着没用，你也可以选择保留   
		 #（注：由于我在Fastlane文件导出的包名与打包时间相关，每次都不一样，所以每打一次就会不断增加ipa包）     
		 rm -rf /Users/LiuDeHua/jenkins/workspace/CIProject/JenkinsDemo/ios/build
    ;;
    Android)
         echo "❣️❣️❣️️开始Android打包，成功后自动上传到蒲公英"
         # 这个目录就是jenkins拉取的工程所在的目录，只有这个目录下可以打包
		 cd /Users/langke/jenkins/workspace/CIProject/JenkinsDemo/android
    ;;
    *)
    	echo "❌❌❌ Arguments error! Please check your shell script!!!"
    	exit
    ;;
esac
# 更新bundle命令（fastlane官方建议的），如果没使用bundle，这一行直接去掉
/usr/local/bin/bundle update
# 打包本地的fastlane脚本，如果没使用bundle，去掉/usr/local/bin/bundle exec即可 
/usr/local/bin/bundle exec fastlane UploadToPgyer
```

> 注：以上的路径，比如 `/Users/LiuDeHua/jenkins/workspace/CIProject/JenkinsDemo/ios` 都可以自己利用`terminal`进入到你的工程根目录下，然后使用 `pwd` 命令查看并复制粘贴即可，每个人的用户名、项目名一般都不一样，因此路径也不一样，但原理都是一样的。

- **7.2.6 构建后操作（不需要，可跳过）**

## 八 Jenkins打包报错

- ❌  `(iOS)`  error: Can't find 'node' binary to build React Native bundle

> 这个报错一般针对`React Native`工程，原生项目不会。


✅ 打开`xcode`, `Build Phases` -> `Bundle React Native code and images`

里面的脚本默认看起来是下面这个样子👇

```
export NODE_BINARY=/usr/local/bin/node
../node_modules/react-native/scripts/react-native-xcode.sh
```

在`terminal`上输入：

```
which node
```

比如我电脑上💻的输出结果是：

```
/usr/local/bin/node
```

将`/usr/local/bin/node`替换脚本中的`node`即可，即最终结果看起来如下👇：

```
export NODE_BINARY=node
../node_modules/react-native/scripts/react-native-xcode.sh
```

- ❌ ❌ `Android`

```
FAILURE: Build failed with an exception.
 
What went wrong:
Execution failed for task ':app:bundleReleaseJsAndAssets'.
A problem occurred starting process 'command 'node''
```

✅✅ 本地进入到项目的 `./android`目录下，执行

- 方法一

```
./gradlew --stop
```

答案源自: [github](https://github.com/facebook/react-native/issues/6875#issuecomment-215854946)

如果失败请尝试`方法二`

在`android/app/build.gradle`里添加`nodeExecutableAndArgs : ["/usr/local/bin/node"]`，如下:

- 方法二

```
project.ext.react = [
    entryFile: "index.js",
    nodeExecutableAndArgs : ["/usr/local/bin/node"] // add this line to jenkins package
]
```


然后重新尝试打包，应该就`OK`了。

