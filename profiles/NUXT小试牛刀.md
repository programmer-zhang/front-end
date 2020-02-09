## NUXT简介与优势
* [官方文档](https://zh.nuxtjs.org/)
* 基于vue.js
* 自动代码分层
* 服务端渲染
* 强大的路由功能，支持自动生成路由，支持异步数据
* 静态文件服务
* ES6/ES7 语法支持
* 打包和压缩 JS 和 CSS
* HTML头部标签管理
* 本地开发支持热加载
* 集成ESLint
* 支持各种样式预处理器： SASS、LESS、 Stylus等等

## 安装与使用
* 快速入手
	* 利用 `vue-cli`
	* `vue init nuxt-community/starter-template <project-name>`
	* `npm install`
	* `npm run dev`
* 徒手搭建
	* 新建 `package.json`
	
	```
	{
	  "name": "my-app",
	  "scripts": {
		    "dev": "nuxt"
		}
	}
	```
	* `npm install --save nuxt`
	* `npm run dev`

## 流程图与生命周期
 ![流程图](../images/nuxt-schema.png)
 
 * 调用 nuxtServerInit 方法
	* 当请求打入时，最先调用的即是 nuxtServerInit 方法，可以通过这个方法预先将服务器的数据保存，如已登录的用户信息等。另外，这个方法中也可以执行异步操作，并等待数据解析后返回。
* Middleware 层
	* 读取 nuxt.config.js 中全局 middleware 字段的配置，并调用相应的中间件方法 匹配并加载与请求相对应的 layout 调用 layout 和 page 的中间件方法
* 调用 validate 方法
	* 在这一步可以对请求参数进行校验，或是对第一步中服务器下发的数据进行校验，如果校验失败，将抛出 404 页面。
* 调用 fetch 及 asyncData 方法
	* 这两个方法都会在组件加载之前被调用，它们的职责各有不同， asyncData 用来异步的进行组件数据的初始化工作，而 fetch 方法偏重于异步获取数据后修改 Vuex 中的状态。

## 目录结构
* assets
	* 用于存放静态文件，会被打包的静态文件
* components
	* 存放项目中的各种组件
* layouts
	* 创建自定义的页面布局，可以在这个目录下创建全局页面的统一布局，或是错误页布局。如果需要在布局中渲染 `pages` 目录中的路由页面，需要在布局文件中加上 `<nuxt/>` 标签。
* middleware
	* 存放中间件，也是nuxt的核心
* pages
	* 该文件下的文件，Nuxt.js 会根据目录的结构生成 vue-router 路由
* plugins
	* 在这个目录中放置自定义插件，在根 Vue 对象实例化之前运行。例如，可以将项目中的埋点逻辑封装成一个插件，放置在这个目录中，并在 nuxt.config.js 中加载。
* static
	* 与assets相似，也是存放静态文件，区别是该文件夹下的静态文件不会被打包构建，会映射到根目录下
* store
	* 存放vuex状态树，谨慎使用
* nuxt.config.js
	* nuxt的配置文件

## nuxt.config.js配置
* head: 可以在这个配置项中配置全局的 head ，如定义网站的标题、 meta ，引入第三方的 CSS、JavaScript 文件等
* build: 配置 Nuxt.js 项目的构建规则，即 Webpack 的构建配置，如通过 vendor 字段引入第三方模块，通过 plugin 字段配置 Webpack 插件，通过 loaders 字段自定义 Webpack 加载器等。通常我们会在 build 的 vendor 字段中引入 axios 模块，从而在项目中进行 HTTP 请求（ axios 也是 Vue.js 官方推荐的 HTTP 请求框架）
	* 通过build vendor引入的模块，避免import引入多次重复打包，减少应用bundle的体积
* css: 引入全局的 CSS 文件，之后每个页面都会被引入,也可在head中以cdn的形式引入
* route: 可以在此配置路由的基本规则，以及进行中间件的配置。例如，你可以创建一个用来获取 User-Agent 的中间件，并在此加载。
* loading: Nuxt.js 提供了一套页面内加载进度指示组件，可以在此配置颜色，禁用，或是配置自定义的加载组件。
* env: 配置用来在服务端和客户端共享的全局变量。
* plugins: 引入项目需要的插件
	* 只在浏览器运行的插件，将引入的插件属性设置为ssr:false
	* 只在服务端运行的插件


