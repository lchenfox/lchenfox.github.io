## 解析步骤

- 在任意目录，这里以桌面为例，创建文件夹`CrashParser`（名字随意）

```
cd ~/Desktop && mkdir CrashParser && cd CrashParser
```

- 将`xxx.crash`、`xxx.app.dSYM`文件拖到`CrashParser`文件夹中

- 解析并查看符号化（解析）后的奔溃信息

```
cp /Applications/Xcode.app/Contents/SharedFrameworks/DVTFoundation.framework/Versions/A/Resources/symbolicatecrash . && export DEVELOPER_DIR="/Applications/XCode.app/Contents/Developer" && ./symbolicatecrash xxx.crash xxx.app.dSYM > ParsedSymbol.crash && open ParsedSymbol.crash
```

其中，将`xxx.crash xxx.app.dSYM`中的`xxx`替换成对应的完整奔溃文件名和`dSYM`文件名，其它的都可以不用修改，直接执行即可。

例如，下面的奔溃文件在解析前为`UnparsedSymbol.crash`，`dSYM`文件夹为`Demo.app.dSYM`

```
➜  CrashParser ls
Demo.app.dSYM        UnparsedSymbol.crash
➜  CrashParser cp /Applications/Xcode.app/Contents/SharedFrameworks/DVTFoundation.framework/Versions/A/Resources/symbolicatecrash . && export DEVELOPER_DIR="/Applications/XCode.app/Contents/Developer" && ./symbolicatecrash UnparsedSymbol.crash Demo.app.dSYM > ParsedSymbol.crash && open ParsedSymbol.crash
```

在执行之后，变成如下

```
➜  CrashParser ls
Demo.app.dSYM  ParsedSymbol.crash  UnparsedSymbol.crash symbolicatecrash
```

其中`ParsedSymbol.crash`就是最终解析后的符号化文件，`symbolicatecrash`是我们用于执行解析的*可执行文件*，这个*可执行文件*来源于`Xcode`。

## 注意点

通常情况下，可执行文件`symbolicatecrash`的路径`/Applications/Xcode.app/Contents/SharedFrameworks/DVTFoundation.framework/Versions/A/Resources/symbolicatecrash`是不变的，如果你不确定这个路径是否正确，通过以下命令可以查看

```
find /Applications/Xcode.app -name symbolicatecrash -type f
```

如果出现错误`Error: "DEVELOPER_DIR" is not defined at ./symbolicatecrash line 69.`，执行如下命令

```
export DEVELOPER_DIR="/Applications/XCode.app/Contents/Developer"
```