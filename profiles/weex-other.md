### weex complie 命令
* 打包指令(将指定目录下的vue文件进行编译为js的操作)

```
weex compile <源码文件或者目录(src/xxx.vue)> <生成打包后的目录地址(dist)>
```
* 对编译后的js进行压缩混淆(-m 减小很大体积)

```
weex compile src dist -m
```

### 预览项目
* weex-toolkit 支持预览你当前开发的 weex 页面(.we或者.vue)，你只需要指定预览的文件路径即可：

```
weex src/foo.vue
```

* 浏览器会自动弹出页面，这个时候你可以看到你所编辑的 Weex 页面的具体效果和页面布局。如果你使用 Playground 扫描右边的二维码，就能够看到 Weex 在 Android/IOS 设备上的效果了。

* 如果你需要预览整个项目目录，你可以输入这样的命令:

```
weex src --entry src/foo.vue
```
你需要在传入的参数指定预览的目录和入口文件。

### weex 资源文件
* weex 访问 native资源文件
* native 访问 weex 打包后的资源文件

### weex-no-router
* 使用 weex-navigator
* 准备使用 Weex 来做新的移动端项目，但是在页面跳转方面有些疑惑。现有以下几种方案：
	* Android 就一个 Activity ，页面跳转逻辑都通过 vue-router 来实现。
	* 每个页面都是一个 Activity ，每个Activity加载各自的 bundle.js 文件，数据通过 storage 模块传输。
	* 通过navigator模块实现页面跳转。

### 安卓壳子使用url访问静态资源打开
* 安卓版本太高可能会导致 http 协议的页面打不开，被禁止，需要降级处理