* 移除 Xcode 运行安装 APP 产生的缓存文件(DerivedData)
只要重新运行Xcode就一定会重新生成，而且会随着运行程序的增多，占用空间会越来越大。删除后在重新运行程序可能会稍微慢一点，建议定期清理。

路径：`~/Library/Developer/Xcode/DerivedData`
释放空间：`0~xx GB`


* 移除 APP 打包的ipa历史版本(Archives)
删除后不可恢复，文件夹是按照日期排列的，所以如果你不想全部删除，就只保留最新的几个版本就好了，个人建议全部删除。

路径：
~/Library/Developer/Xcode/Archives
释放空间：`0~xx GB`


* 移除 APP 打包的app icon历史版本(Archives)
删除后不可恢复，文件夹是Bundle Idenifier排列的，然后再按照archive的版本号排列的，如果你看每个版本内的内容，其实就是你的app icon，个人建议全部删除。

路径：
`~/Library/Developer/Xcode/Products/`
释放空间：`30M`


* 移除模拟器的缓存数据(Devices)
模拟器的相关数据。每个版本的模拟器占用的内存空间大约为10M左右。每个文件夹里包含的就是一个特定系统版本的设备的数据。每个文件夹对应哪个设备可以在其下device.plist中查看。删除之后，如果立即运行程序会报错，先关闭Xcode，再重新打开程序，运行即可。运行该路径下会立马生成模拟器对应版本的文件。

路径：
`~/Library/Developer/CoreSimulator/Devices/`
释放空间 `约12GB`，个人建议全部删除
* 移除对旧设备的支持(iOS DeviceSupport)
一般是占用内存空间最大的文件夹，即使全部删，再连接设备调试时，会重新自动生成。一般iOS只向下兼容两个版本就可以了，所以我移除了9.0以下的所有版本。

路径：
`~/Library/Developer/Xcode/iOS DeviceSupport`
释放空间： `约3GB/版本`


* 移除 Xcode 中的无效的插件(Plug-ins)
因为之前你可能安装了一些 Xcode 的插件，比如HighlightSelectedString、VVDocumenter-Xcode等非常方便好用的第三方插件，在Xcode升级到version 8.0以后，就失效了，Xcode在内部已经集成了类似的方法，所以之前安装的也都没有用了，但是还在原来的位置占用着内存空间，建议删除。

路径：
`~/Library/Application Support/Developer/Shared/Xcode/Plug-ins`
如果你曾经没有安装过插件，那么可能不存在此路径。

* 移除旧版本的模拟器支持
如果你不小心安装了很多个版本的模拟器，那么你可以删除一些旧版本的。但是当你需要旧版本的模拟器时，就需要重新下载了。建议留下1~2个版本就好了，其余的都删了吧。

路径：
~/Library/Developer/CoreSimulator/Profiles/Runtimes/
释放空间 ≈ 2.5GB/版本


* 移除 playground 的项目缓存(XCPGDevices)
删除后可重新生成，可以全部删除。再次运行程序会缓存。

路径：
`~/Library/Developer/XCPGDevices/`
我从使用Xcode几年没删除过此文件夹也就占用约300M内存空间，可依据个人喜好操作。


* 移除旧的文档(Docsets)
删除后不可恢复，该目录下存储的为开发文档，一般有三个文件`com.apple.adc.documentation.iOS.docset(1.68GB)`、`com.apple.adc.documentation.OSX.docset(2.62GB)`和`com.apple.adc.documentation.Xcode.docset(256.4M)`，如果你只做iOS开发，其实你可以把OSX.docset删除掉的，因为它占用了2.62GB的内存。

路径：
`~/Library/Developer/Shared/Documentation/DocSets`
整体所占空间约`4.56GB`


* 移除模拟器中的SDK版本(iPhoneSimulator.sdk)
不可恢复，操作请慎重。我个人的此路径下的只有最新版本的sdk，除非当你有多个版本的sdk再酌情删除。

路径:
`~/application/Xcode.app/Contents/Developer/Platforms/``iPhoneSimulator.platform/Developer/SDKs/`
占用空间约4GB，删除时请慎重
Tips：

经过以上步骤大约可以释放出了20GB以上的磁盘空间，这对内存吃紧的Mac Pro来说已经很是有帮助了。欢迎大家提供更多我不知道的方法。