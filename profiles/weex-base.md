<span style="display: inline-block;width: 100%; text-align: center; font-size: 30px;">weex基础使用文档</span>

> 浏览[官方文档](https://weex.apache.org/zh/)后阅读此文更佳

# 写在前面
## 阅读本文您将收获
* 差异化
	* Weex 和 传统Web 的平台差异
	* weex-toolkit和weexpack的区别
* 必要条件
* 快速开始
	* 安装相关开发环境
	* 安装 weex-cli
	* 使用weex-cli创建项目
	* 下载项目依赖包
	* 预览项目
* 开发
	* 运行过程
	* 文件结构
	* 导航方式
	* 数据传递
	* 数据通信 stream模块
	* 语法差异
* Todo List

# 差异化

### Weex 和 传统Web 的平台差异

* weex 是跨平台解决方案，web 仅是作为一个开发环境
* weex 不存在DOM
	* 没有 `Element` , `Event` 等对象
	* 只有 有限的事件类型 和 `native` 端常用事件类型，如 `Longpress`, `Appear`等
* weex 不存在BOM
	* 没有 `document` , `window` 等对象
	* 提供自身对象获取设备部分数据 `WXEnvironment`
* weex的运行环境以原生应用为主，在Android和iOS环境中渲染出来的是原生的组件，而不是DOM元素。
* weex 提供了原生设备调用 API
	* [《clipboard 剪切板》](https://weex.apache.org/zh/docs/modules/clipboard.html?spm=a2c7j.-zh-guide-platform-difference.0.0.1fb6400eP0TKTY)
	* [《navigator 导航控制》](https://weex.apache.org/zh/docs/modules/navigator.html?spm=a2c7j.-zh-guide-platform-difference.0.0.1fb6400eP0TKTY)
	* [《storage 本地存储 》](https://weex.apache.org/zh/docs/modules/storage.html?spm=a2c7j.-zh-guide-platform-difference.0.0.1fb6400eP0TKTY)

### weex-toolkit和weexpack的区别
* weex-toolkit 初始化的项目是针对开发单个 Weex 页面而设计的，也就是说这样的项目只包括单个页面开发需要的东西，比如前端页面源文件、webpack 配置、npm 脚本等。项目产生的输出就是一个 JS Bundle 文件，可以自由的进行部署。
* weexpack 是初始化一个完整的 App 工程，包括 Android 和 iOS 的整个 App 起步，前端页面只是其中的一部分。这样的项目最终产出是一个 Android App 和一个 iOS App。

# 必要条件
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

---
---

# 快速开始
## 安装相关开发环境
* 安装 node 开发环境
* 安装 Android & IOS 开发环境

## 安装 weex-cli
* `npm install weex-toolkit -g`

## 使用weex-cli创建项目
* `weex create demo-project`
* 填写相关配置信息

## 下载项目依赖包
* 进入项目后 `npm install`

## 预览项目
### 浏览器端预览
* `npm start`
* 执行指令后会自动打开浏览器的页面窗口，但是建议使用真机进行预览，部分样式和代码 web 端与 native 端差距较大

### native 端预览
* 编译成 JS Bundle 文件 
	* `weex compile <源码目录或文件> <打包文件存放目录或文件>`
* 压缩编译
	* `weex compile <源码目录或文件> <打包文件存放目录或文件> -m` 
* 配置 Nginx 
* 移动应用客户端访问

---
---
# 开发
## 运行过程
* 本地编写 Vue 代码，生成 web 页面
* weex 形成 JS bundle
* 在云端，通过网络请求或预下发的方式将 JS bundle 传递到用户的 移动应用客户端
* 在移动应用客户端里，WeexSDK 会准备好一个 JavaScript 引擎，并且在用户打开一个 Weex 页面时执行相应的 JS bundle
* 各种命令发送到 native 端进行界面渲染或数据存储、网络通信、调用设备功能、用户交互响应

## 文件结构
* |- src  // 源码目录
* |- node_modules   // 依赖包
* |- dist		// 存放编译好的 js 文件
* |- build	// 存放npm build 时的 js 文件，可在 package.json 文件中配置
* |- package.json	// 项目的配置和依赖库文件
* |- webpack.config.js    // webpack 打包配置文件
* |- config.js	// 项目的相关配置文件，你可以在这个文件中配置切换不同的环境

## 导航方式
### Vue-Router
* 暂不做讲解

### weex-navigator
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

## 数据传递 
### Vue的方式
* `props`
* `this.$emit('fun', data)`

### storage
* 永久保存，在 H5/web 端 实际采用的是 `HTML5 LocalStorage API`
* 限制： 
	* H5/web 端 体积最大为5M
	* Android 和 IOS 端没有限制
* 相关API
	* setItem(key, value, callback)
	* getItem(key, callback)
	* removeItem(key, callback)
	* length(callback)
	* getAllKeys(callback)

### 地址拼接
* 利用 `weex.config.bundleUrl`
* 使用方式

```
// 路由携带方式
navigator.push({
  url: 'http://192.168.xxx.xxx:8081/tmp/detail.js?id=1',
  animated: 'true'
})
// 获取参数
getUrlParam() {
  let name, value;
  let str = weex.config.bundleUrl; //取得整个地址栏
  let num = str.indexOf("?");
  str = str.substr(num + 1); //取得所有参数   

  let arr = str.split("&"); //各个参数放到数组里
  for (var i = 0; i < arr.length; i++) {
    num = arr[i].indexOf("=");
    if (num > 0) {
      name = arr[i].substring(0, num);
      value = arr[i].substr(num + 1);
      this.paramsDic[name] = value;
    }
  }
}
```

### BroadcastChannel
* 源码定义

```
declare interface BroadcastChannel = {
  name: string,  // 频道名称
  postMessage: (message: any) => void;  // 广播消息
  onmessage: (event: MessageEvent) => void;  // 接收消息
  close: () => void;  // 关闭通信
}
```

* 通信过程

![](../images/BroadcastChannel.png)

* 使用方式

```
// 广播消息
const broadcast = new BroadcastChannel('testChannel')
broadcast.postMessage('this is amessage.')
modal.toast({ message: '消息已发送' })

// 接收消息
let self = this
const broadcast = new BroadcastChannel('testChannel')
broadcast.onmessage = function(event) {
	modal.toast({ message: '消息已接收' })
	self.testData = event.data
}
```

### 借用 native 做中转桥
* vue -> native -> vue

## 数据通信 stream模块
### 请求过程
* weex中发送fetch请求时，会先经过native内置的stream模块，并由stream模块向服务器发送请求。

### 请求实例 `fetch(options, callback, progressCallback)` 
* `@options`, 请求的配置选项，支持以下配置
	* `method`: string, HTTP 请求方法，值为 GET/POST/PUT/DELETE/PATCH/HEAD
	* `url`: string, 请求的 URL | string
	* `headers`: string, HTTP 请求头
	* `type`: string, 响应类型：json，text 或是 jsonp(在 native 原生实现中其实与 json 相同)
	* `body`: string, HTTP 请求体, 仅支持string类型的数据，不支持JSON，GET仅支持URL传参，Body的格式 `'param1=p1&param2=p2'`
* 默认 `Content-Type` 是 `application/x-www-form-urlencoded`
* `@callback`, 响应结果回调，回调函数将收到如下的 response 对象：
	* `status`: number, 返回的状态码
	* `ok`: boolean, 如果状态码在 200-299 之间就为 true
	* `statusText`: string, 状态描述文本
	* `data`: string, 返回的数据，如果请求类型是 json 和 jsonp，则它就是一个 object ，否则是一个 string。
	* `headers`: object, HTTP 响应头
* `@progressCallback` , function, 请求过程中的请求进度.
	* `readyState`: number, 当前状态，1: 请求连接中；2: 返回响应头中；3: 正在加载返回数据
	* `status`: number, 返回的状态码
	* `length`: number, 已经接受到的数据长度. 你可以从响应头中获取总长度
	* `statusText`: string, 状态描述文本
	* `headers`: object, HTTP 响应头

### 使用示例
* GET
	
```
let self = this
stream.fetch({
	method: 'GET',
	url: GET_URL,
	type:'json'
}, function(req) {
	if(!req.ok){
		self.getResult = "request failed";
	}else{
		console.log('get:'+req);
		self.getResult = JSON.stringify(req.data);
	}
},function(response){
	console.log('get in progress:'+response.length);
	self.getResult = "bytes received:"+response.length;
});
```
* POST	
	
```
let self = this
stream.fetch({
	method: 'POST',
	url: POST_URL,
	type:'json',
	body: stringData
}, function(req) {
if(!req.ok){
	self.postResult = "request failed";
}else{
	console.log('get:'+JSON.stringify(req));
	self.postResult = JSON.stringify(req.data);
}
},function(response){
	console.log('get in progress:'+response.length);
	self.postResult = "bytes received:"+response.length;
});
```

## 语法差异
### HTML 标签
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
	* `<web>` 用于在weex页面中嵌入一张网页内容，和HTML中的 `<iframe>` 作用类似。

### CSS样式
* 支持 px 和 wx 长度单位
* 支持 行内 和 class属性，用id不支持，真机失效
* 不支持样式继承
* 不支持行内样式,用 flex 布局解决
* 不支持层级 z-index, 层级叠加根据书写顺序展示
* 不支持 box-shadow
* 不支持 css 三角形
* 不支持 样式缩写
* 部分支持伪类样式
	* Weex 支持四种伪类：`active`, `focus`, `disabled`, `enabled` 
	* 所有组件都支持 `active`
	* 只有 input 组件和 textarea 组件支持 `focus` , `enabled`, `diabled`
* 标准 CSS 中的空格和回车问题在真机中也会存在
* 部分详细约束
	* `<textarea>` 须设置rows，默认值为2，否则只展示两行
	* `<textarea>` 属性 `placeholder-color` 设置 placeholder 颜色
	* `<image>` 必须设置 width 和 height 属性才可以展示
	* `<image>` width 宽度撑满 750px;
	* `<image>` 按图片宽高比撑满 `flex:1;`
	* `<list>` 和 `<scroller>` 不支持同一方向的嵌套列表或滚动条
	* `<text>` css样式 `lines:1;` 设置超出隐藏显示省略号部分失效
	* `position` 的新属性 `stiky`, 用于滚动的header固定

### JavaScript
* weex 中不支持 Promise 的 finally 方法，支持 then 和 catch
* v-if 支持 v-show 不支持
* 冒泡机制
	* weex 中冒泡机制默认不开启，需要手动书写 `bubble = true`
	* 阻止冒泡 `event.stopPropagation();`
* 支持手势操作监听

---
---
# Todo List
* 预览项目流程简化
* stream 封装
* 通用UI组件库封装
* 全局通信封装
* 压缩包体积
* 整合跨平台兼容性问题
* JSService
* debugger