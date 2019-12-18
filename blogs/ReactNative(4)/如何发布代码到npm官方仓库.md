---
title: 【RN】如何发布代码到npm官方仓库
date: 2019-12-18 13:07:23
categories: React Native
tags: 
- React Native
- npm
- 私有仓库
---

## 前言

有时候我们想把自己的组件像在`github`上提供的以`npm`方式安装到本地的方式给别人用，我们就可以到[npm官方网站](https://www.npmjs.com/)注册自己的账号并以`CLI`的形式将自己的代码发布到`npm官方仓库`。

但是，目前来说，我们可以发布到`npm官方仓库`的组件或库都是**公开的**，这意味着是开源的，也就是**任何人都可以搜索到你发布的这个仓库并且安装使用**。

官方目前提供`2`种方式，一种是免费的，也就是我们刚才说的公开的，这种方式不会限制你发布多少包到`npm`，你发布十个、百个甚至上千个都行，只要你开心，想发就发；另一种方式是收费的，具体的价格[npm官方价格说明](https://www.npmjs.com/products)，当然，这种收费的好处是，我们可以发布**私有仓库**，这种**私有仓库**别人未经授权是看不到摸不着。如果你只是想把自己的组件开源给别人用，发扬开源精神，那么使用免费的就可以了，如果你不想提供给别人用，比如说是公司内部的一些业务组件、功能组件等，那么除了`npm`官方收费的方式以外，也有一些其他创建**私有npm仓库**的方式。但是在这篇文章中，只讲发布**免费版**的仓库。

## 注册

首先第一件事就是注册，**你必须拥有一个npm账号才能发布包**，这是一个必须的步骤。

- 1.[npm官方网站](https://www.npmjs.com/)，点击`Sign Up`

![Sign Up.png](https://i.loli.net/2019/12/18/ZhRYnaHIl1MNcG3.png)

- 2.填写注册信息，然后点击`Create an Account`

![Sign Up.png](https://i.loli.net/2019/12/18/Vwar9YP3XB5OGy4.png)

- 3.登录邮箱，将类似红框中的链接复制到你的浏览器中，回车，验证完毕

![Verify Email.png](https://i.loli.net/2019/12/18/4KdZJtUsyYQmqXA.png)

- 4.注册成功，你将看到如下界面

![Success.png](https://i.loli.net/2019/12/18/R5H94eqfsn3J1Uh.png)

## 创建&发布包

- 1.打开`terminal`，随便找一个文件夹作为测试仓库，执行`npm adduser`

![init.png](https://i.loli.net/2019/12/18/TDpUZVaYlxEArmb.png)

> 图中的`langke_npm`是我自己随便创建的文件夹，你也可以自己随意命名创建，开心就好。另外，你也可以使用`npm login`来代替命令`npm adduser`，两者并没有什么区别，如果不信，那就看这里[见识一下官方npm login & npm adduser](https://docs.npmjs.com/cli/adduser.html)。然后，输入用户名、密码、邮箱，注意，输入密码的时候你是看不见的，这是`terminal`为了保护你的安全，做了隐藏，出现类似`Logged in as langke007 on http://registry.npmjs.org/.`，你已经登录成功。

- 2.初始化仓库，执行`npm init`

![npm init.gif](https://i.loli.net/2019/12/18/LEk45XIV8bUORMA.gif)

> 执行`npm init`后，会填写一些相应信息，根据提示填写即可，没什么难度，结束后会生成一个`package.json`文件，里面就包含你填写的所有信息。

- 3.创建`README.md`、`index.js`文件

![instruction.png](https://i.loli.net/2019/12/18/nEWZzSJy3u9Hq6V.png)

- 4.发布，执行`npm publish`

```
➜  langke_npm npm publish
npm notice
npm notice 📦  langke_npm_demo@1.0.0
npm notice === Tarball Contents ===
npm notice 60B  index.js
npm notice 292B package.json
npm notice 33B  README.md
npm notice === Tarball Details ===
npm notice name:          langke_npm_demo
npm notice version:       1.0.0
npm notice package size:  410 B
npm notice unpacked size: 385 B
npm notice shasum:        730c17ff5fd22e05d0a1e05088b74066667c292d
npm notice integrity:     sha512-+80RanxwwDwTv[...]ZJDWYN+EDm37w==
npm notice total files:   3
npm notice
+ langke_npm_demo@1.0.0
```

![npm publish.gif](https://i.loli.net/2019/12/18/6HcM3zBEU5DCPIk.gif)

> 进入到网页，你就可以看到刚才所发布的包，具体见上图。如果发布遇到类似`npm ERR! no_perms Private mode enable, only admin can publish this module`的错误，应该是镜像源的问题，使用官方镜像源，执行`npm config set registry http://registry.npmjs.org`。

## 使用刚才发布的包

我这里是`React Native`项目，但是都是一样的道理，到根目录下执行

- 1.安装

```
npm install langke_npm_demo
```

然后在项目中的文件里导入

- 2.使用

```
import {printHello} from 'langke_npm_demo';

...

printHello()
```

## 取消发布

使用命令`npm unpublish <package-name> -f`来取消发布，这样的话，你刚才发布的包就会被撤回，等于没有发布！

![npm unpublish.gif](https://i.loli.net/2019/12/18/nP2IotwfBvAJESK.gif)

## 在新电脑或者新目录下更新并发布已有的npm包

如果你换了一台新电脑，或者删除了刚才所创建的`npm`包，那么可以在新电脑或者新目录下，执行

```
npm install <package-name>
```

安装结束后，你会看到一个`node_modules`文件夹，里面有你刚才创建的`npm`包，然后一切都和前面的发布一样，修改任意你想修改的文件，在有`package.json`文件的目录下，执行`npm publish`就可以了。但是要记住，每一次发布`npm`包都需要修改版本号，否则你将发布失败！具体修改版本号后面也有常用的配套命令介绍，往下看，没兴趣的话，就到此为止。

## 关联github

有时候我们看到别人发布到`npm`官方仓库的包，具有如下红框中的部分，也就是关联到`github`，包括`Issues`、`Pull Requests`等等

![sample.png](https://i.loli.net/2019/12/18/zvshy1cAudSNgfJ.png)

为了让我们的公共仓库也能关联到`github`，我们可以在创建`npm`包的时候填写`git repository`一栏填写`github`的某个仓库的`https`地址，如下

![npm github.gif](https://i.loli.net/2019/12/18/iqIJEYM1fSTalsQ.gif)

> 这里我已经提前在`github`上创建了一个`langke-npm-demo1`的仓库了，所以关联地址的时候，之后复制过去就好了。

## 一些常用npm命令介绍

- 查看当前登录的npm用户

```
npm whoami
```

- 查看npm配置

```
npm config ls
```

- 查看当前使用的npm镜像源

```
npm config get registry
```

- 切换npm镜像源

```
npm config set registry 【这里是镜像源url】
```

### npm修改版本号

比如要将版本号从`1.0.0`修改为`2.0.0`，有`2`种方式

#### 一 手动修改

直接修改`package.json`文件中的版本号

原来的`package.json`内容

```
{
  "name": "react-native-demo-for-npm",
  "version": "1.0.0",
  "description": "this is just a npm demo",
  ......
}
```

修改为

```
{
  "name": "react-native-demo-for-npm",
  "version": "2.0.0",
  "description": "this is just a npm demo",
  ......
}
```

#### 二 命令修改

- 1.修改指定版本号

在有`package.json`的当前目录下，使用

```
npm version 新版本号
```

比如

```
npm version 2.0.0
```

这样的话，在发布之前，就可以将版本号从`1.0.0`修改为`2.0.0`了。

- 2.由npm命令生成有序版本号

2.1) 修改主版本号，如果当前版本号是`1.0.0`，要修改为`2.0.0`，使用

```
npm version major
```

> 这种方式一般用于包的大幅度改动，如增加了大量了`API`或者发生其他大量的内容修改。

2.2) 修改次版本号，比如当前版本号是`1.0.0`，要修改为`1.1.0`，使用

```
npm version minor
```

> 这种方式一般用于包的小幅度改动，比如增加了一些`API`的前后兼容，扩展等等。

2.3）修改补丁版本号，比如当前版本号是`1.0.0`，要修改为`1.0.1`，使用

```
npm version patch
```

> 这种方式一般用于打补丁，也就是一些`bug`修复，没有做其他太大的内容变化。

- 查看已发布的npm包所有版本

```
npm view react-native-demo-for-npm versions
```

### 撤销已经发布的包

- 一 撤销整个包

1.1）如果已经将`npm`包发布到远程`npm`仓库，但是又想撤销发布

```
npm unpublish <package-name> -f
```

比如，已经发布到`npm`仓库的包名叫做`react-native-demo`，那么运行

```
npm unpublish react-native-demo -f
```

则`react-native-demo`就从`npm`仓库上删除了，你也无法再从`npm`仓库搜索到这个包。

1.2）如果开启了`2FA(two-factor authentication)`，那么就需要在`publish`后面加上`--otp=123456`，这里的`123456`是手机上`app（app名称叫做 Authenticator）`获取的授权码

```
npm unpublish --otp=123456 react-native-demo -f
```

- 二 撤销具体的版本

2.1）如果想要撤销已经发布的具体版本，则可以使用

```
npm unpublish <package-name>@<version>
```

比如，现在有`2`个版本，分别是`v1.0.0`、`v1.2.0`，想要撤销`v1.2.0`，可以

```
npm unpublish react-native-demo@1.2.0
```

2.2）同样地，如果开启了`2FA(two-factor authentication)`，那么就需要在`publish`后面加上`--otp=123456`，这里的`123456`是手机上`app（app名称叫做 Authenticator）`获取的授权码

```
npm unpublish react-native-demo@1.2.0 --otp=123456
```

> 注意：撤销整个包或具体的版本只有在从开始发布内的`72`小时内有效，具体见 [官网：Unpublishing packages from the registry](https://docs.npmjs.com/unpublishing-packages-from-the-registry)

