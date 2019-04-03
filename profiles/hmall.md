## 记一次惨痛的Vue-cli + VueX + SSR经历
> 前言介绍

> 总部提出新项目，大致需求就是APP内置一个H5商城，于是开始出差去总部极限开发

### 技术选型
* 项目语言：HTML、CSS、JavaScript
* 项目框架：Vue.js
* 项目搭建脚手架：vue-cli
* 工程化工具：webpack、Sass、Npm
* 源码管理：gitlab
* 运行环境：Browser & Node(PM2)
* 第三方服务：GrowingIO、高德地图

### 技术方案
* 非单页面应用，多页面站点，极限开发，直接使用Vue-cli
* 产品考虑SEO，上VueX和SSR
* 便于开发，使用sass等工程化工具
* 根据项目需要，封装user.js(针对用户信息存储), base.js(基础公共JS文件), url.js(针对url的操作), http.js(二次封装axios)
* 根据项目需求，抽象组件(compoments)和插件(widgets)注册到Vue实例
* 根据项目需要，合理设置Router和page
* 根据接口及环境需要，设置必要的环境变量和接口地址
* 根据开发和生产需要，添加必要的依赖

### Node服务器版本项目架构
* 整个网站的架构采用横向分层，从上往下越来越抽象，引用关系由上至下，拒绝由下至上的引用。
	* 语言&环境
	* 框架层
	* 业务公共层
	* 应用层
![](../images/hmall-basic.jpg)

#### 语言&环境
* 语言：采用 Html 控制各个模块的结构；采用 Sass 做样式的预处理；采用 ECMAScript 6 来开发逻辑和交互，然后通过 Webpack 和 Babel 将高级版本的 JS 编译成当下流行浏览器能够解析的 ECMAScript 5。
* 环境：Web 前端的代码主要运行在浏览器端，但是也能在 Node 环境运行，通过 Vue-ssr Node 端插件，同样的前端代码也可以通过服务器端将 Html 渲染出来。正式的部署中，Node 的进程管理是通过 PM2（process manager 2），它可以帮你检查进程的健康情况，并提供强大的接口，让你很容易的了解 Node 在服务器中的运行情况。