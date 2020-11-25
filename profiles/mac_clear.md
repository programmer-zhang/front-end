## 清理MAC存储空间
> 每个使用mac电脑的开发者都会头疼的一件事---电脑用着很爽，但是就是存储空间经常出现不足。

> 小编也经常遇到这个问题，之前总是寄希望于那些傻瓜式操作的软件，这次直接通过终端，一次性清理了大量存储空间，那种感觉又回来了~

## 阅读本文您将收获
* 终端查看文件夹占用存储量
* 傻瓜式清理空间软件测评
* 终极清理存储空间方法

## 如何查看文件占用空间
### 图形化操作式
* 苹果LOGO -> 关于本机 -> 存储空间

* 在上面的图片中，黄色是照片，红色是应用程序，浅蓝色是消息，紫色是音乐，深蓝色是邮件，浅蓝色是iCloud Drive，灰色是系统。

* 而其他是最大的存储占用，在我们的示例中为38.55GB，你可能想知道其他是什么。

* 尽管文件类型的大多数主要类别都很简单，但“其他”种类却可能是一个谜。如果不是音乐，文档，视频，照片或应用，那会是什么？

* 系统将标签“其他”应用于不完全适合这些类型的文件，例如安装程序包，缓存文件，旧备份，应用程序扩展，临时文件等。大多数都是不需要的，但是它们必须存储在某个地方，因此它们会被转储到 "其他" 类别中。

### 终端指令式
* 显示当前目录占用空间大小：

```
du -sh
```
* 显示当前文件夹下所有条目占据内存空间大小：

```
du -sh *
```
* 查看指定文件夹占据空间大小：

```
du -sh /dir
```
* 查找占据空间最大的文件：

```
du -h --max-depth=1
```
* 查看目录下所有文件的大小并按照大小排序

```
du -sh * | sort -rh 
```

## 清理存储空间①使用第三方软件进行清理

## 清理存储空间②终端清理无用文件


## 清理存储空间③终端清理 Xcode 占用的空间
### 移除 Xcode 运行安装 APP 产生的缓存文件(DerivedData)
只要重新运行Xcode就一定会重新生成，而且会随着运行程序的增多，占用空间会越来越大。删除后在重新运行程序可能会稍微慢一点，建议定期清理。

**路径**：`~/Library/Developer/Xcode/DerivedData`

**释放空间**：`0~xx GB`

### 移除 APP 打包的 ipa 历史版本(Archives)
删除后不可恢复，文件夹是按照日期排列的，所以如果###你不想全部删除，就只保留最新的几个版本就好了，个人建议全部删除。

**路径**：`~/Library/Developer/Xcode/Archives`

**释放空间**：`0~xx GB`

### 移除 APP 打包的 app icon 历史版本(Archives)
删除后不可恢复，文件夹是Bundle Idenifier排列的，然后再按照archive的版本号排列的，如果你看每个版本内的内容，其实就是你的app icon，个人建议全部删除。

**路径**：`~/Library/Developer/Xcode/Products/`

**释放空间**：`30M`

### 移除模拟器的缓存数据(Devices)
模拟器的相关数据。每个版本的模拟器占用的内存空间大约为10M左右。每个文件夹里包含的就是一个特定系统版本的设备的数据。每个文件夹对应哪个设备可以在其下device.plist中查看。删除之后，如果立即运行程序会报错，先关闭Xcode，再重新打开程序，运行即可。运行该**路径**下会立马生成模拟器对应版本的文件。

**路径**：`~/Library/Developer/CoreSimulator/Devices/`

**释放空间** `约12GB`，个人建议全部删除

### 移除对旧设备的支持(iOS DeviceSupport)
一般是占用内存空间最大的文件夹，即使全部删，再连接设备调试时，会重新自动生成。一般iOS只向下兼容两个版本就可以了，所以我移除了9.0以下的所有版本。

**路径**：`~/Library/Developer/Xcode/iOS DeviceSupport`

**释放空间**： `约3GB/版本`

### 移除 Xcode 中的无效的插件(Plug-ins)
因为之前你可能安装了一些 Xcode 的插件，比如HighlightSelectedString、VVDocumenter-Xcode等非常方便好用的第三方插件，在Xcode升级到version 8.0以后，就失效了，Xcode在内部已经集成了类似的方法，所以之前安装的也都没有用了，但是还在原来的位置占用着内存空间，建议删除。

**路径**：`~/Library/Application Support/Developer/Shared/Xcode/Plug-ins`

如果你曾经没有安装过插件，那么可能不存在此。

### 移除旧版本的模拟器支持
如果你不小心安装了很多个版本的模拟器，那么你可以删除一些旧版本的。但是当你需要旧版本的模拟器时，就需要重新下载了。建议留下1~2个版本就好了，其余的都删了吧。

**路径**：`~/Library/Developer/CoreSimulator/Profiles/Runtimes/`

**释放空间**：`约2.5GB/版本`


### 移除 playground 的项目缓存(XCPGDevices)
删除后可重新生成，可以全部删除。再次运行程序会缓存。

**路径**：`~/Library/Developer/XCPGDevices/`

### 移除旧的文档(Docsets)
删除后不可恢复，该目录下存储的为开发文档，一般有三个文件`com.apple.adc.documentation.iOS.docset(1.68GB)`、`com.apple.adc.documentation.OSX.docset(2.62GB)`和`com.apple.adc.documentation.Xcode.docset(256.4M)`，如果你只做iOS开发，其实你可以把OSX.docset删除掉的，因为它占用了2.62GB的内存。

**路径**：`~/Library/Developer/Shared/Documentation/DocSets`

**释放空间**：`约4.56GB`

### 移除模拟器中的SDK版本(iPhoneSimulator.sdk)
不可恢复，操作请慎重。我个人的此路径下的只有最新版本的sdk，除非当你有多个版本的sdk再酌情删除，删除时请慎重。

**路径**:

```
~/application/Xcode.app/Contents/Developer/Platforms/
iPhoneSimulator.platform/Developer/SDKs/
```

**释放空间**：`约4GB`