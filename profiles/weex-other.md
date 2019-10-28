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
* 使用 weex-navigator
	* 把一个weex页面URL压入导航堆栈中
	
	```
	push({
	    url :""        //要压入的 Weex 页面的 URL
	    animated:""    //"true" 示意为页面压入时需要动画效果，"false" 则不需要，默认值为 "true"。注意，一定要是字符串类型的，千万不能写成布尔类型
	}, callback(){
	    //回调
	})
	```

	* 把当前Weex页面弹出导航堆栈中

	```
	pop({
	    animated:""    //"true" 示意为页面压入时需要动画效果，"false" 则不需要，默认值为 "true"。注意，一定要是字符串类型的，千万不能写成布尔类型
	}, callback(){
	    //回调
	})
	```

### 安卓壳子使用url访问静态资源打开
* 安卓版本太高可能会导致 http 协议的页面打不开，被禁止，需要降级处理

## weex 语法差异
### HTML 标签
* 遵循weex官方文档，部分标签样式在weex真机中不保证能够正常使用
	* 如 span
* weex 标签真正解析后会解析成不同的标签
	* text -> p
* 结构不支持不规范嵌套
	* 如：text 嵌套 text
* 部分详细的约束
	* `<a>` 不可直接添加文字，需要用 `<text>` 来显示文字
	* `<input>` 禁用须设置 `disabled=true` 不能简写
	* `<input>` 两个属性不同 `maxlength` 数值类型 `max-length` 输入字符串内容,测试失效,并且按照输入内容计算,不是字节
	* `<div>` 内直接添加文本，css会失效；weex 中不可滚动

### CSS样式
* 支持 px 和 wx 长度单位
* 支持 行内 和 class属性，用id不支持，真机失效
* 样式不支持继承
* 样式不支持行内,用 flex 布局解决
* 标准 CSS 中的空格和回车问题在真机中也会存在
* 不支持层级 z-index
* 不支持 box-shadow
* 不支持 css 三角形
* 不支持 样式缩写
* 部分支持伪类样式
* 部分详细约束
	* `<textarea>` 须设置rows，默认值为2，否则只展示两行
	* `<textarea>` 属性 `placeholder-color` 设置placeholder颜色
	* `<image>` 必须设置 width 和 height 属性才可以展示
	* `<image>` width 宽度撑满 750px;
	* `<image>` 按图片宽高比撑满 `flex:1;`
	* `<list>` 和 `<scroller>` 不支持同一方向的嵌套列表或滚动条
	* `<text>` 属性 `lines:1;` 设置超出隐藏显示省略号部分失效

### JavaScript
* weex 中不支持 Promise 的 finally 方法，支持 then 和 catch
* v-if 和 v-show