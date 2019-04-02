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
* 产品考虑SEO，上SSR
* 便于开发，使用sass等工程化工具
* 根据项目需要，封装user.js(针对用户信息存储), base.js(基础公共JS文件), url.js(针对url的操作), http.js(二次封装axios)
* 根据项目需求，抽象组件(compoments)和插件(widgets)
* 
