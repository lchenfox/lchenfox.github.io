---
title: 【iOS】 用sonar-swift实现SonarQube代码质量扫描
date: 2022-03-19
categories:
- iOS
- Objective-C
- Swift
tags:
- sonar-swift
- SonarQube
- 代码扫描
---

## 前言

[SonarQube](https://www.sonarqube.org/)是一个开源的代码质量扫码分析工具，分为四个版本，分别为`Community`、`Developer`、`Enterprise`、`Data Center`，其中`Community`社区版免费开源，其他三个版本都是收费版，一般来说，社区版就符合大多数开发者的需求，针对很多语言都可以免费扫描。

但是，遗憾的是，针对`iOS`，社区版是不支持`Objective-C`、`Swift`扫描的，只有开发版及以上收费版本才支持！

因此，要使用社区版，我们不得不寻找一些开源的插件，以实现在`SonarQube`上`iOS`的代码质量扫描分析。此文就是以[sonar-swift插件](https://github.com/Idean/sonar-swift)来实现`iOS`的代码扫描分析，它同时支持`Objective-C`和`Swift`。

## 安装`java 11`

为什么需要安装`java 11`而不是`java 8`或者其他`java`版本？因为`SonarQube scanner`和`SonarQube server`支持`java 11`，其他的`java`版本不被`SonarQube`官方支持！见 [Prerequisites and Overview - Supported Platforms](https://docs.sonarqube.org/latest/requirements/requirements/)。

前往`java`官网，下载`java 11`，[点击前往Java SE Development Kit 11.0.14](https://www.oracle.com/java/technologies/downloads/#java11)。*不知道如何安装的话，查看 [Installation of the JDK on macOS](https://docs.oracle.com/en/java/javase/11/install/installation-jdk-macos.html#GUID-2FE451B0-9572-4E38-A1A5-568B77B146DE)。*

> 注意：在未安装`java 11`之前，我使用的是`java 8`，导致`SonarQube server`启动失败。

如何确定安装成功？执行

```
➜  ~ java --version
java 11.0.14 2022-01-18 LTS
Java(TM) SE Runtime Environment 18.9 (build 11.0.14+8-LTS-263)
Java HotSpot(TM) 64-Bit Server VM 18.9 (build 11.0.14+8-LTS-263, mixed mode)
```
说明`java 11`已经安装成功。

## 安装`SonarQube`

**注意：请不要使用最新的`SonarQube`版本，因为`sonar-swift`的`jar`包并不支持最新的版本如`9.3`。因此，建议使用官方提供的长期支持和维护的版本`SonarQube 8.9.7 LTS`。**

- 下载`SonarQube 8.9.7`。

[👉下载官方长期支持维护版本：SonarQube 8.9.7 LTS](https://binaries.sonarsource.com/Distribution/sonarqube/sonarqube-8.9.7.52159.zip)。

下载之后，将其放到你想要放的目录，这个目录可以是任何目录，比如我的就是放到用户下面的`CICD`文件夹下。

- 启动`SonarQube Server`。

```
➜  ~ $HOME/CICD/sonarqube-8.9.7.52159/bin/macosx-universal-64/sonar.sh start
Starting SonarQube...
Started SonarQube.
```


- 登录 [http://localhost:9000](http://localhost:9000)。

初始账号密码都是：`admin`。首次登录系统会要求你修改密码，比如你可以随便修改成`admin123`。

## 安装`sonar-scanner`

强烈推荐使用`brew`安装

```
brew install sonar-scanner
```

查看是否安装成功

```
➜  ~ sonar-scanner --version
INFO: Scanner configuration file: /opt/homebrew/Cellar/sonar-scanner/4.6.2.2472_1/libexec/conf/sonar-scanner.properties
INFO: Project root configuration file: NONE
INFO: SonarScanner 4.6.2.2472
INFO: Java 11.0.12 Homebrew (64-bit)
INFO: Mac OS X 12.2.1 aarch64
```

## 其他插件安装

#### 一 安装`xcpretty`

查看是否安装，如果已经安装，可以跳过

```
➜  ~ xcpretty --version
0.2.2
```

安装

```
git clone https://github.com/Backelite/xcpretty.git
cd xcpretty
git checkout fix/duration_of_failed_tests_workaround
gem build xcpretty.gemspec
sudo gem install --both xcpretty-0.2.2.gem
```

> 注意：不要使用`sudo gem install -n /usr/local/bin xcpretty`这样安装，按照`sonar-swift`官方推荐的上述安装方式，兼容`SonarQube`。

#### 二 安装`swiftlint`

```
brew install swiftlint
```

查看安装

```
➜  ~ swiftlint --version
0.46.3
```

#### 三 安装`Tailor`

```
brew install tailor
```

查看安装

```
➜  ~ tailor --version
0.12.0
```

#### 四 安装`slather`

`slather`可能会要求相应的`ruby`版本，如果失败，升级`ruby`即可。使用`brew`安装`ruby`

```
brew install ruby
```

由于系统也默认安装了`ruby`，这时可能会提示如下

```
.......................
.......................
==> ruby
By default, binaries installed by gem will be placed into:
  /opt/homebrew/lib/ruby/gems/3.1.0/bin

You may want to add this to your PATH.

ruby is keg-only, which means it was not symlinked into /opt/homebrew,
because macOS already provides this software and installing another version in
parallel can cause all kinds of trouble.

If you need to have ruby first in your PATH, run:
  echo 'export PATH="/opt/homebrew/opt/ruby/bin:$PATH"' >> ~/.zshrc

For compilers to find ruby you may need to set:
  export LDFLAGS="-L/opt/homebrew/opt/ruby/lib"
  export CPPFLAGS="-I/opt/homebrew/opt/ruby/include"
```

按照提示，添加以上路径，如

```
echo 'export PATH="/opt/homebrew/opt/ruby/bin:$PATH"' >> ~/.zshrc
export LDFLAGS="-L/opt/homebrew/opt/ruby/lib"
export CPPFLAGS="-I/opt/homebrew/opt/ruby/include"
```

然后`source ~/.zshrc`就可以了。查看`ruby --version`，可以看到已经是最新版本。

```
sudo gem install -n /usr/local/bin slather
```

查看安装

```
➜  ~ slather version  
slather 2.7.2
```

> 强烈建议不要使用官方的安装方式：`sudo gem install slather`，因为有可能会出现命令找不到的情况，即安装完成后执行`slather version`会报错`command not found: slather`。

#### 五 安装`lizard`

首先查看一下是否安装了`pip`

```
which pip
```

**如果未安装，则需要先安装`pip`:**

1、 依次执行以下两个命令

```
curl https://bootstrap.pypa.io/get-pip.py -o get-pip.py
python3 get-pip.py
```

2、 如果遇到如下警告

```
Installing collected packages: pip
  WARNING: The scripts pip, pip3 and pip3.8 are installed in '/Users/langke/Library/Python/3.8/bin' which is not on PATH.
  Consider adding this directory to PATH or, if you prefer to suppress this warning, use --no-warn-script-location.
```

添加以下命令到`.zshrc`并执行`source ~/.zshrc`即可

```
export PATH=$HOME/Library/Python/3.8/bin:$PATH
```

3、 安装成功后，可以查看一下`pip`版本

```
➜  ~ pip --version
pip 22.0.3 from /Users/long.chen28/Library/Python/3.8/lib/python/site-packages/pip (python 3.8)
```

**接着安装`lizard`**

```
sudo pip install lizard
```

#### 六 安装`OCLint`

[参考OCLint Installation](https://oclint-docs.readthedocs.io/en/stable/intro/installation.html)。

前往 [OCLint Releases](https://github.com/oclint/oclint/releases) 下载支持你当前`Xcode`版本的`OCLint`版本，放到任意你想放的目录下，比如我的是放到`CICD`文件夹下(和`SonarQube`平级）。

由于这里使用的是`Xcode 13.2.1`，因此下载`oclint-22.02-llvm-13.0.1-x86_64-darwin-macos-12.2-xcode-13.2.tar.gz`即可

然后打开`vi .zshrc`文件

```
OCLint_PATH=$HOME/CICD/oclint-22.02
export PATH=$OCLint_PATH/bin:$PATH
```

保存退出后，执行`source $HOME/. zshrc`。

查看安装

```
➜  ~ oclint --version
OCLint (https://oclint.org):
  OCLint version 22.02.
  Built Feb 20 2022 (07:40:36).
```

> 注意：一定不要使用`brew`安装`OCLint`，因为使用`brew`安装的`OCLint`可能不是最新版本，或者不兼容当前的`Xcode`，导致无法扫描！比如我使用`brew`安装的是`20.11`，而`20.11`并不支持`Xcode 13.2.1`！如果你能确保使用`brew`安装的`OCLint`兼容`Xcode`当前版本，并没有任何问题。`OCLint`版本对应支持的`Xcode`版本 [OCLint Releases](https://github.com/oclint/oclint/releases)。

## 配置`sonar-project.properties`

这个文件配置很简单，根据需要按下面格式配置即可。

```
# Swift
sonar.swift.project=OCSwiftDemo.xcodeproj
sonar.swift.workspace=OCSwiftDemo.xcworkspace
sonar.swift.appScheme=OCSwiftDemo
sonar.swift.excludedPathsFromCoverage=.*Tests.*,.*Specs.*
sonar.swift.simulator=platform=iOS Simulator,name=iPhone 13,OS=15.2
sonar.swift.tailor.config=--no-color --max-line-length=100 --max-file-length=500 --max-name-length=40 --max-name-length=40 --min-name-length=4
sonar.swift.skipTests=OCSwiftDemoUITests

# OC
sonar.objectivec.project=OCSwiftDemo.xcodeproj
sonar.objectivec.workspace=OCSwiftDemo.xcworkspace 
sonar.objectivec.appScheme=OCSwiftDemo 
sonar.objectivec.excludedPathsFromCoverage=.*Tests.*,.*Specs.*
sonar.objectivec.simulator=platform=iOS Simulator,name=iPhone 13,OS=15.2
sonar.objectivec.tailor.config=--no-color --max-line-length=100 --max-file-length=500 --max-name-length=40 --max-name-length=40 --min-name-length=4

# Common
sonar.projectKey= OCSwiftDemoProjKey
sonar.projectName=OCSwiftDemo
# Be careful! If you'd like to upload source files that contains Pods directory, 
# you must remove Pods from .gitignore file. Otherwise nothing will be uploaded.
sonar.sources=OCSwiftDemo
sonar.projectDescription="Scanning code smells and upload to SonarQube." 
sonar.sourceEncoding=UTF-8 

# SonarQube
# sonar.host.url=http://localhost:9000
# sonar.login=dbe0f3944f65563094e86a81bf041a4edfbee873

sonar.host.url=your sonar url
sonar.login=your sonar account
sonar.password=your sonar password
```

## 配置`run-sonar-swift.sh`

这个文件只需要从插件官网直接下载即可，不需要修改任何配置。

## 扫描

执行

```
bash run-sonar-swift.sh -nounittests -v
```

即可在`SonarQube`网页上浏览结果。遗憾的是，不知道是由于`sonar-swift`插件的原因还是`SonarQube`的原因，扫描结果都是以`坏味道`的方式呈现出来。

## 错误处理

- 报错`1`

```
Computing coverage report
slather coverage --input-format profdata --cobertura-xml --output-directory sonar-reports --scheme OCSwiftDemo OCSwiftDemo.xcodeproj
/opt/homebrew/lib/ruby/gems/3.1.0/gems/slather-2.7.2/lib/slather/project.rb:506:in `find_binary_files': No scheme named 'OCSwiftDemo' found in /Users/long.chen28/Desktop/OCSwiftDemo/OCSwiftDemo.xcodeproj (StandardError)
	from /opt/homebrew/lib/ruby/gems/3.1.0/gems/slather-2.7.2/lib/slather/project.rb:456:in `configure_binary_file'
	from /opt/homebrew/lib/ruby/gems/3.1.0/gems/slather-2.7.2/lib/slather/project.rb:336:in `configure'
	from /opt/homebrew/lib/ruby/gems/3.1.0/gems/slather-2.7.2/lib/slather/command/coverage_command.rb:59:in `execute'
	from /opt/homebrew/lib/ruby/gems/3.1.0/gems/clamp-1.3.2/lib/clamp/command.rb:66:in `run'
	from /opt/homebrew/lib/ruby/gems/3.1.0/gems/clamp-1.3.2/lib/clamp/subcommand/execution.rb:18:in `execute'
	from /opt/homebrew/lib/ruby/gems/3.1.0/gems/clamp-1.3.2/lib/clamp/command.rb:66:in `run'
	from /opt/homebrew/lib/ruby/gems/3.1.0/gems/clamp-1.3.2/lib/clamp/command.rb:140:in `run'
	from /opt/homebrew/lib/ruby/gems/3.1.0/gems/slather-2.7.2/bin/slather:17:in `<top (required)>'
	from /usr/local/bin/slather:25:in `load'
	from /usr/local/bin/slather:25:in `<main>'
ERROR - Command 'slather coverage --input-format profdata --cobertura-xml --output-directory sonar-reports --scheme OCSwiftDemo OCSwiftDemo.xcodeproj' failed with error code: 1
```

*解决*: `General -> Edit Scheme -> Test -> Options -> Code Coverage`打勾。


- 错误`2`

```
xcodebuild: error: Scheme OCSwiftDemo is not currently configured for the test action.
```

*解决*：在执行脚本的时候用 `bash run-sonar-swift.sh -nounittests -v`（推荐），也可以在`run-sonar-swift.sh`中将 `unittests="on"`改为`unittests=""`。

- 错误`3`

```
oclint: error: compilation contains multiple jobs:
```

*解决*：在`xcodebuild`命令中添加`COMPILER_INDEX_STORE_ENABLE=NO`参数，如下

```
xcodebuild -scheme OCSwiftDemo -workspace OCSwiftDemo.xcworkspace clean && xcodebuild -scheme OCSwiftDemo -workspace OCSwiftDemo.xcworkspace COMPILER_INDEX_STORE_ENABLE=NO -configuration Debug -arch x86_64 -sdk iphonesimulator15.2 | xcpretty -r json-compilation-database -o compile_commands.json
```

> `Tips1`：要使用`-sdk iphonesimulator15.2`参数，但是不知道当前支持的版本，可以使用`xcodebuild -showsdks`命令。

## 附录

[个人博客](https://www.clcoder.com/)。