---
title: verdaccio+ngrok发布npm私有仓库
date: 2019-12-20 17:07:23
categories: React Native
tags: 
- React Native
- npm
- 私有仓库
- verdaccio-ngrok
---

## 介绍

在上一篇([如何发布代码到npm官方仓库](https://www.clcoder.com/2019/12/18/%E5%A6%82%E4%BD%95%E5%8F%91%E5%B8%83%E4%BB%A3%E7%A0%81%E5%88%B0npm%E5%AE%98%E6%96%B9%E4%BB%93%E5%BA%93/#more))文章中，我们介绍了如何将自己的代码发布到`npm`官方仓库，通过那种方式，我们可以将自己写的任何组件开源给别人用，和我们日常在`github`上使用的三方库是一个道理。我们也说过，我们发布的组件或包都是开源的，任何人都可以访问并且可以在[npm官方仓库](https://www.npmjs.com/)搜索到的，虽然`npm`官方提供的收费版也可以发布私有仓库，特别方便，但是，太`TM`贵了！所以，今天我们来继续介绍另一种不使用`npm官方提供的收费版私有仓库`的方式，来搭建属于我们自己的仅仅公司内部可使用的`npm`私有仓库 -- `verdaccio & ngrok`。

#### verdaccio

一个轻量级的[私有npm代理注册表](https://github.com/verdaccio/verdaccio)，你可以将`verdaccio`理解为一个**私有npm仓库服务器**，因为使用`verdaccio`，你可以将`npm`包发布到你的本地仓库，此时你自己的电脑就是一台`npm`私有仓库服务器，你可以在你的电脑发布`npm`包，并且通过`npm install`来安装使用。

#### ngrok

[ngrok](https://ngrok.com/)就是一个[方向代理](https://baike.baidu.com/item/%E5%8F%8D%E5%90%91%E4%BB%A3%E7%90%86)，这里的主要作用是实现[内网穿透](https://baike.baidu.com/item/%E5%86%85%E7%BD%91%E7%A9%BF%E9%80%8F)，因为使用`verdaccio`搭建本地私有`npm`仓库服务器后，你的电脑就是作为服务器使用，你可以在你的电脑上下载（`npm install`）、使用你发布的私有`npm`仓库，但是另一台电脑或服务器上是不能访问的，也就是除了你，其他任何人都没法使用你发布的`npm`私有仓库，如果你想要别人也能够使用你发布的`npm`私有仓库的话，你必须将类似由`verdaccio`服务生成的类似`http://localhost:4873`的`url`暴露给他人，通过`ngrok`你可以将你自己电脑上的本地`url`映射到大千世界，以至于别人能够通过映射的`新url`访问你的`npm`私有仓库。

## 搭建npm私有本地仓库

### 安装

首先要全局安装`verdaccio`，使用如下命令

```
npm install --global verdaccio
```

> 也可以使用简写`npm install -g verdaccio`，两者并没区别。

安装成功后，接着启动`verdaccio`

```
verdaccio
```

启动之后，你应该就能看到

![verdaccio.png](https://i.loli.net/2019/12/20/U8CBgPXKbti9d6x.png)

图中的`http://localhost:4873`就是启动后的本地服务的`url`，在浏览器中打开它，你应该能够看到

![verdaccio url.png](https://i.loli.net/2019/12/20/5zcIlg2ZHdS3raQ.png)

内容如下

```
No Package Published Yet.
To publish your first package just:
1. Login
npm adduser --registry http://localhost:4873

2. Publish
npm publish --registry http://localhost:4873

3. Refresh this page.
```

> 注意：虽然这里登陆&发布都在后缀添加了`--registry http://localhost:4873`，也就是为了指定发布在本地服务`http://localhost:4873`上，这样也是为了误操作发布到官方`npm`仓库。但是为了后面不添加后缀就可以和正常的`npm`操作一样，不用添加任何后缀，我们会直接更新`npm`默认源，见下面👇`更新默认npm源`。

### 更新默认npm源

设置`http://localhost:4873`为`npm`默认源

```
npm config set registry http://localhost:4873
```

为什么要设置`http://localhost:4873`为`npm`默认源？

首先要清楚的是，我们电脑上`npm`的镜像源是官方的`http://registry.npmjs.org/`，因此当我们使用`npm publish`或者`npm install`的时候，都是发布或安装到`npm`官方仓库，如果这样的话，使用`verdaccio`还有什么意义？我们还怎么发布私有库？所以我们要本地发布、安装`npm`私有仓库，就必须要使用`verdaccio`的镜像源，也就是刚才的本地`url` **--** `http://localhost:4873`，我们可以使用下面的命令查看当前的镜像源

```
npm config get registry
```

更新默认`npm`镜像源后，你应该看到能够看到输出的镜像源是

```
➜  ~ npm config get registry
http://localhost:4873/
```

### 创建私有npm用户

```
npm adduser
```

> 注意：这里使用`npm adduser`（你也可以使用`npm login`）之后会要求你输入`用户名`、`密码`、`邮箱`三个信息，然后会创建一个`npm本地账户`，为什么说是`npm本地账户`？因为这个账号的镜像源是`http://localhost:4873`而不是官方的`http://registry.npmjs.org/`，因此如果你使用这个`npm本地账户`到官方网站登录的话，你会发现你无法登录，换句话说，这个账号属于本地所有，不被官方`npm`所承认，两者是不同的`npm`账号。

## 创建本地私有npm包

创建`npm`包和我们官方的`npm`创建发布到`npm`官方仓库的命令基本一样，如果你还不熟悉发布公有仓库到`npm`官方仓库，建议还是先看一下[如何发布代码到npm官方仓库](https://www.clcoder.com/2019/12/18/%E5%A6%82%E4%BD%95%E5%8F%91%E5%B8%83%E4%BB%A3%E7%A0%81%E5%88%B0npm%E5%AE%98%E6%96%B9%E4%BB%93%E5%BA%93/#more)

#### 初始化

在你想要创建`npm`私有仓库的文件夹目录下，我的是`/Users/langke/TestReactNative/verdaccio-dir`，执行

```
npm init
```

填写相应信息，我的输出如下

```
This utility will walk you through creating a package.json file.
It only covers the most common items, and tries to guess sensible defaults.

See `npm help json` for definitive documentation on these fields
and exactly what they do.

Use `npm install <pkg>` afterwards to install a package and
save it as a dependency in the package.json file.

Press ^C at any time to quit.
package name: (verdaccio-dir) first-verdaccio-npm-demo
version: (1.0.0)
description: This is the first private npm demo for verdaccio.
entry point: (index.js)
test command:
git repository:
keywords: verdaccio-npm-demo
author: langke
license: (ISC)
About to write to /Users/langke/TestReactNative/verdaccio-dir/package.json:

{
  "name": "first-verdaccio-npm-demo",
  "version": "1.0.0",
  "description": "This is the first private npm demo for verdaccio.",
  "main": "index.js",
  "scripts": {
    "test": "echo \"Error: no test specified\" && exit 1"
  },
  "keywords": [
    "verdaccio-npm-demo"
  ],
  "author": "langke",
  "license": "ISC"
}


Is this OK? (yes) yes
```

创建一个`index.js`和`README.md`文件，并添加内容（这一步非必须，我这里只是掩饰）

```
➜  verdaccio-dir vi index.js
➜  verdaccio-dir ls
index.js     package.json
➜  verdaccio-dir vi README.md
➜  verdaccio-dir cat README.md
# Getting started

Hello, world!
```

`package.json`文件内容如下：

```
{
  "name": "first-verdaccio-npm-demo",
  "version": "1.0.0",
  "description": "This is the first private npm demo for verdaccio.",
  "main": "index.js",
  "scripts": {
    "test": "echo \"Error: no test specified\" && exit 1"
  },
  "keywords": [
    "verdaccio-npm-demo"
  ],
  "author": "langke",
  "license": "ISC"
}
```

## 发布本地npm包

再次提醒，这里发布的`npm`包只在自己的电脑上，也就是本地的`http://localhost:4873（这是我们刚才用来替换npm官方的registry源）`，发布的是一个在本地私有服务器上的一个私有`npm`包。此时，只有自己能够查看、下载、安装，别人没有任何权限的。

发布命令和正常`npm`命令一样

```
npm publish
```

输出类似如下

```
npm notice
npm notice 📦  first-verdaccio-npm-demo@1.0.0
npm notice === Tarball Contents ===
npm notice 91B  index.js
npm notice 321B package.json
npm notice 33B  README.md
npm notice === Tarball Details ===
npm notice name:          first-verdaccio-npm-demo
npm notice version:       1.0.0
npm notice package size:  455 B
npm notice unpacked size: 445 B
npm notice shasum:        df639fd12218accfa1949cb933e496ddea020078
npm notice integrity:     sha512-N4TmHO2xcNSL9[...]SINM/KD+TR5EA==
npm notice total files:   3
npm notice
+ first-verdaccio-npm-demo@1.0.0
```

到这里，说明本地的`npm`私有库发布成功！

在刚才打开`http://localhost:4873`的页面上，刷新页面，可以看到

![npm publish.png](https://i.loli.net/2019/12/20/SGtie3qgHLvf5nu.png)

看到了没，这个`first-verdaccio-npm-demo`就是我们刚刚发布的`npm`私有库，现在自己就可以像正常使用`npm`官方的命令那样安装到本地依赖使用了。

## 使用

找到你的项目（最好是测试项目），安装试试(我使用的是`yarn`，你也可以使用`npm`)

```
yarn add first-verdaccio-npm-demo
```

然后出现类似下面输出

```
yarn add v1.17.3
[1/4] 🔍  Resolving packages...
[2/4] 🚚  Fetching packages...
[3/4] 🔗  Linking dependencies...
warning "@react-native-community/eslint-config > @typescript-eslint/eslint-plugin@1.13.0" has incorrect peer dependency "eslint@^5.0.0".
warning "@react-native-community/eslint-config > @typescript-eslint/parser@1.13.0" has incorrect peer dependency "eslint@^5.0.0".
warning "@react-native-community/eslint-config > eslint-plugin-react@7.12.4" has incorrect peer dependency "eslint@^3.0.0 || ^4.0.0 || ^5.0.0".
warning "@react-native-community/eslint-config > eslint-plugin-react-native@3.6.0" has incorrect peer dependency "eslint@^3.17.0 || ^4 || ^5".
warning "@react-native-community/eslint-config > @typescript-eslint/eslint-plugin > tsutils@3.17.1" has unmet peer dependency "typescript@>=2.8.0 || >= 3.2.0-dev || >= 3.3.0-dev || >= 3.4.0-dev || >= 3.5.0-dev || >= 3.6.0-dev || >= 3.6.0-beta || >= 3.7.0-dev || >= 3.7.0-beta".
[4/4] 🔨  Building fresh packages...
success Saved lockfile.
success Saved 1 new dependency.
info Direct dependencies
└─ first-verdaccio-npm-demo@1.0.0
info All dependencies
└─ first-verdaccio-npm-demo@1.0.0
✨  Done in 2.73s.
```

![yarn add.png](https://i.loli.net/2019/12/20/FO8SRPxKa3yXdtM.png)

太棒了，在`package.json`中的`dependencies`依赖中终于成功安装我们的`npm`私有库`first-verdaccio-npm-demo`了，继续看下`node_modules`中

![node_modules.png](https://i.loli.net/2019/12/20/XNHfdu1MDsSKgWm.png)

现在，尽情地在你的项目中使用这个组件吧

```
import {putMsg} from 'first-verdaccio-npm-demo';

putMsg(); // Great, you got a private npm repository!
```

但是，这样还不够啊！只有我自己可以使用，那别人（比如公司内部的团队成员）要怎么才能安装我的这个私有库呢？是时候祭出我们的尚方宝剑`ngrok`了。

## 使用ngrok让本地npm私有库公有化

#### 安装ngrok

```
npm i ngrok -g
```

#### 暴露4873端口

```
ngrok http 4873
```

输出类似如下

![ngrok.png](https://i.loli.net/2019/12/20/DZ5vGkVdRqtFzLb.png)

在上图中我们可以看到`http://8f76570f.ngrok.io -> http://localhost:4873（当然还有https）`，其中`http://8f76570f.ngrok.io`就是我们通过`ngrok`反向代理将本地`http://localhost:4873`公开给外部环境，这样，别人也可以访问你刚才发布的`npm`私有库`first-verdaccio-npm-demo`。你可以在另一台电脑上访问`http://8f76570f.ngrok.io`，它和我们刚才在自己电脑上访问`http://localhost:4873`是一样的，并且能够看到刚刚发布的私有库。

#### 使用私有库

现在，别人能够访问我们刚才发布的私有库了，但是，要想使用刚才发布的私有库还需要在相应电脑上设置`npm`镜像源为`http://8f76570f.ngrok.io`，因为默认的是官方的`http://registry.npmjs.org/`，这很好理解。

在要访问当前电脑上的`npm`私有库，在另一台电脑上更新`npm`镜像源

```
npm config set registry http://8f76570f.ngrok.io
```

然后安装

```
npm install first-verdaccio-npm-demo
```

这样，就能够实现除了在本电脑的服务上使用`npm`私有库外，团队成员也能够使用相应私有库了。

## 优缺点

虽然使用 `verdaccio + ngrok` 来部署`npm`私有库，但是也有一些缺点。

#### 优点

- 实现npm私有库发布，不对外公开（除非你把代理源比如`http://8f76570f.ngrok.io`给外部人员），也可以在`~/.config/verdaccio/config.yaml`添加访问权限，默认`access: $all`，这样最保险（`verdaccio`的具体配置，自己去官方网站看即可）

这里还是顺便提一下`access`字段，如果你设置为`access: $authenticated`，意味着你在网页打开也必须要登录才能查看、安装（也就是我们上面说的`npm adduser`创建的账户），如果你已经在`terminal`上登录，并且在执行`npm install`的时候还是报错类似如下：

```
yarn add v1.17.3
[1/4] 🔍  Resolving packages...
error An unexpected error occurred: "http://localhost:4873/first-verdaccio-npm-demo: 
authorization required to access package first-verdaccio-npm-demo".
info If you think this is a bug, please open a bug report with the information 
provided in "/Users/langke/TestReactNative/fuckmap/yarn-error.log".
info Visit https://yarnpkg.com/en/docs/cli/add for documentation about this command.
```

那么请执行：

```
npm config set always-auth=true
```

这样的话，你只要登录你的账号（无论是浏览器还是终端），就能查看/下载了。重要的是，如果你用`npm adduser（或npm login）`创建了多个账号，比如`test1/test123、test2/test234`，那么在授权的时候，你可以登录其中任意一个的账号都可以访问和下载（因为你注册的多个账号都在你本机的`verdaccio`服务上）。

- 可以使用和官方`npm`一样的命令，比如`npm version patch`

- 操作、配置简单，基本无难度

- verdaccio本身自带小型数据库，可缓存已经`npm install`过的私有库（如果网络不好或获取不到本台电脑上的私有库的话，会从缓存中获取）

#### 缺点

- 可能会经常要切换`npm`代理源，因为有时候你可能会使用官方仓库，就得切换官方或其他代理源，再用私有库的时候又得切换回来（团队成员要安装已更新的`npm`包一样要设置）

- `verdaccio`服务和`ngrok`服务要保持开着，一旦断开，任何人（包括本机）都无法安装和发布，只能重新启动`verdaccio`和`ngrok`服务，对方如果想要更新最新发布的私有库版本，也要重新设置`ngrok`产生的类似``http://8f76570f.ngrok.io``的镜像源（本机如果设置`npm config set registry http://localhost:4873`就不用，因为`http://localhost:4873`是不变的，变的只是映射出去的`http://8f76570f.ngrok.io`）

- ngrok服务器在国外，有时会比较慢

- ngrok代理的镜像源比如`http://8f76570f.ngrok.io`没有固定`url`，每一次执行`ngrok http 4873`，都会改变。因此如果在新电脑或者其他人设置过`http://8f76570f.ngrok.io`作为镜像源，在本机上再次执行`ngrok http 4873`后，对方也要跟着重新设置类似的`npm config set registry http://8f76570f.ngrok.io`操作，这显然很麻烦（因为使用的是免费版，收费版可以代理固定的`url`，但如果那样的话，那为何不使用`npm`官方提供的收费版呢？）

- `http://8f76570f.ngrok.io`在每分钟内访问有限制，访问太快，浏览器会报如下错误(恢复也较快，30秒左右，可自己尝试)：

```
Too Many Connections
Too many connections! The tunnel session '1VF8JMx9QsgS6GcWS9gPBZyQQO4' has violated 
the rate-limit policy of 20 connections per minute by initiating 37 connections in 
the last 60 seconds. Please decrease your inbound connection volume or upgrade to 
a paid plan for additional capacity.

The error encountered was: ERR_NGROK_702
```

## 总结

使用`verdaccio + ngrok`配置起来比较方便，也能迎合`npm`的相关命令，安装和操作都特别简单，`verdaccio`也提供本地缓存，这个很棒。但是速度这一块以及`ngrok`产生的代理源不固定，团队协作安装较为麻烦，这个有时候真的不能忍受（可以看上面的优缺点）。但是，如果能接受的话，这也不失为一个选择。如果你有比`ngrok`更好用的反代理或者其他工具，请告诉我。下一章我会介绍如何通过`npm + github`的方式来创建`npm`私有仓库。


参考文章：[Testing your npm package before releasing it using Verdaccio + ngrok](https://medium.com/strapi/testing-your-npm-package-before-releasing-it-using-verdaccio-ngrok-28e2832c850a)