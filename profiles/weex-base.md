# weex基础使用文档

> 浏览[官方文档](https://weex.apache.org/zh/)后阅读此文更佳

## 写在前面
### 文档内容
* 差异化
	* Weex 和 传统Web 的平台差异
	* weex-toolkit和weexpack的区别
* 必要条件

### 差异化

#### Weex 和 传统Web 的平台差异

* weex 是跨平台解决方案，web 仅是作为一个开发环境
* weex 不存在DOM
	* 没有 `Element` , `Event` 等对象
	* 只有 有限的事件类型 和 `native` 端常用事件类型，如 `Longpress`, `Appear`等
* weex 不存在BOM
	* 没有 `document` , `window` 等对象
	* 提供自身对象获取设备部分数据 `WXEnvironment`
* weex 提供了原生设备调用 API
	* [《clipboard 剪切板》](https://weex.apache.org/zh/docs/modules/clipboard.html?spm=a2c7j.-zh-guide-platform-difference.0.0.1fb6400eP0TKTY)
	* [《navigator 导航控制》](https://weex.apache.org/zh/docs/modules/navigator.html?spm=a2c7j.-zh-guide-platform-difference.0.0.1fb6400eP0TKTY)
	* [《storage 本地存储 》](https://weex.apache.org/zh/docs/modules/storage.html?spm=a2c7j.-zh-guide-platform-difference.0.0.1fb6400eP0TKTY)

#### weex-toolkit和weexpack的区别
* weex-toolkit 初始化的项目是针对开发单个 Weex 页面而设计的，也就是说这样的项目只包括单个页面开发需要的东西，比如前端页面源文件、webpack 配置、npm 脚本等。项目产生的输出就是一个 JS Bundle 文件，可以自由的进行部署。
* weex-pack 是初始化一个完整的 App 工程，包括 Android 和 iOS 的整个 App 起步，前端页面只是其中的一部分。这样的项目最终产出是一个 Android App 和一个 iOS App。

### 必要条件
* 开发环境
	* nodeJS
	* Android 开发环境
	* IOS 开发环境
* 开发工具
	* Sublime Text
	* Android studio
	* Xcode
* 代码管理
	* Github

## 快速开始
### 安装相关开发环境
### 安装 weex-cli
* `npm install weex-toolkit -g`

### 使用weex-cli创建项目
* `weex create demo-project`
* 填写相关配置信息

### 下载项目依赖包
* 进入项目后， `npm install`

### 预览项目
##### 浏览器端预览
* `npm start`
* 执行指令后会自动打开浏览器的页面窗口，但是建议使用真机进行预览，部分样式和代码 web 端与 native 端差距较大

##### native 端预览
* 编译成 JS Bundle 文件 
	* `weex compile <源码目录或文件> <打包文件存放目录或文件>`
* 压缩编译
	* `weex compile <源码目录或文件> <打包文件存放目录或文件> -m` 

## 开发
### 文件结构
### 导航方式
##### VUE-Router
* 暂不做讲解

##### weex-navigator
* 借用 IOS/Android  navigator 模块
* 使用方式
	* 把一个weex页面URL压入导航堆栈中
	
	```
	push({
	    url :""        // 要压入的 Weex 页面的 URL
	    animated:""    // "true" 示意为页面压入时需要动画效果，"false" 则不需要，默认值为 "true"。注意，一定要是字符串类型的，千万不能写成布尔类型
	}, callback(){ 
	    //回调
	})
	```

	* 把当前Weex页面弹出导航堆栈中

	```
	pop({
	    animated:""    // "true" 示意为页面压入时需要动画效果，"false" 则不需要，默认值为 "true"。注意，一定要是字符串类型的，千万不能写成布尔类型
	}, callback(){
	    //回调
	})
	```

### 语法差异
##### HTML 标签
* 遵循weex官方文档，部分标签样式在weex真机中不保证能够正常使用
	* 如 `<span>`
* weex 标签真正解析后会解析成不同的标签
	* `<text>` -> `<p>`
* 结构不支持不规范嵌套
	* 如：`<text>` 嵌套 `<text>`
* `<div>` 在 weex 中不可滚动，需要使用 滚动标签 `<list>` 或者 `<scroller>` 或者 `<waterfall>` 
* 部分详细的约束
	* `<a>` 不可直接添加文字，需要用 `<text>` 来显示文字
	* `<input>` 禁用须设置 `disabled=true` 不能简写
	* `<input>` 两个属性不同 `maxlength` 数值类型 `max-length` 输入字符串内容,测试失效,并且按照输入内容计算,不是字节
	* `<div>` 内直接添加文本，css和双向绑定会失效

##### CSS样式
* 支持 px 和 wx 长度单位
* 支持 行内 和 class属性，用id不支持，真机失效
* 不支持样式继承
* 不支持行内样式,用 flex 布局解决
* 不支持层级 z-index, 层级叠加根据书写顺序展示
* 不支持 box-shadow
* 不支持 css 三角形
* 不支持 样式缩写
* 部分支持伪类样式
* 标准 CSS 中的空格和回车问题在真机中也会存在
* 部分详细约束
	* `<textarea>` 须设置rows，默认值为2，否则只展示两行
	* `<textarea>` 属性 `placeholder-color` 设置 placeholder 颜色
	* `<image>` 必须设置 width 和 height 属性才可以展示
	* `<image>` width 宽度撑满 750px;
	* `<image>` 按图片宽高比撑满 `flex:1;`
	* `<list>` 和 `<scroller>` 不支持同一方向的嵌套列表或滚动条
	* `<text>` css样式 `lines:1;` 设置超出隐藏显示省略号部分失效

##### JavaScript
* weex 中不支持 Promise 的 finally 方法，支持 then 和 catch
* v-if 支持 v-show 不支持
