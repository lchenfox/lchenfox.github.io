---
title: Homebrew error Not a valid ref refs remotes origin master
date: 2021-04-21 18:14:00
categories: iOS
tags: 
- Homebrew 
---


本文参考：[brew 报错 error: Not a valid ref: refs/remotes/origin/master 的解决方法](https://learnku.com/articles/55985)

## 安装`Homebrew`

在终端执行以下命令

```
/bin/bash -c "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/HEAD/install.sh)"
```

## 报错

```
==> Checking for `sudo` access (which may request your password).
Password:
==> This script will install:
/opt/homebrew/bin/brew
/opt/homebrew/share/doc/homebrew
/opt/homebrew/share/man/man1/brew.1
/opt/homebrew/share/zsh/site-functions/_brew
/opt/homebrew/etc/bash_completion.d/brew
/opt/homebrew

Press RETURN to continue or any other key to abort
==> /usr/bin/sudo /usr/sbin/chown -R long.chen28:admin /opt/homebrew
==> Downloading and installing Homebrew...
HEAD is now at 844f15ede Merge pull request #11182 from Rylan12/deprecate-brew-search-casks
error: Not a valid ref: refs/remotes/origin/master
fatal: ambiguous argument 'refs/remotes/origin/master': unknown revision or path not in the working tree.
Use '--' to separate paths from revisions, like this:
'git <command> [<revision>...] -- [<file>...]'
```

## 原因

当安装多次失败时，会由于本地存在缓存，再次安装导致冲突。

## 解决

打开 [链接🔗](https://raw.githubusercontent.com/Homebrew/install/master/uninstall)，复制里面的内容，在终端使用如下命令创建一个`uninstall.rb`文件并将复制的内容粘贴到这个`uninstall.rb`文件里面，保存退出

链接里面的内容如下

```
#!/usr/bin/ruby

STDERR.print <<EOS
Warning: The Ruby Homebrew uninstaller is now deprecated and has been rewritten in
Bash. Please migrate to the following command:
  /bin/bash -c "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/HEAD/uninstall.sh)"

EOS

Kernel.exec "/bin/bash", "-c", '/bin/bash -c "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/HEAD/uninstall.sh)"' + ' uninstall ' +  ARGV.join(" ")
```

```
vi uninstall.rb
```

然后执行

```
ruby uninstall.rb
```

这样就解决了刚才的报错问题，然后再次尝试安装就好。


