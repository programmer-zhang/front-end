# 微信小程序云开发改造
> 文章仅介绍改造过程和部分细节改造，如果您没有小程序开发经验建议先查看官方文档

> 本小程序仅针对从原生小程序迁移到云开发小程序进行改造，目前微信小程序自动支持使用脚手架工具生成云开发小程序，官方文档的使用教程很详细，这里不再赘述

## 写在前面
### 改造背景
球队需要一个管理与报名工具，初期打算前后端分离，小程序端交互和逻辑由本人开发，后端由另一位队友开发，无奈开发初期赶上18年年底行业寒冬，大家工作变动和适应新公司，开发进度缓慢。最后猪年伊始决定直接使用腾讯云服务搭建后台，全部在小程序端实现。

### 必要功能(优先级自上而下递减)
* 踢球活动报名(done)
* 发布踢球活动(done)
* 个人信息(done)
* 看球活动报名(developing)
* 协会公告(developing)
* 调查问卷(done)

### 改造基础
* 微信小程序云开发提供 数据库、云函数、存储库、云调用和 HTTP API ，提供的相关功能足够支撑小程序后端服务
* 小程序的相关数据存储在 云数据库 中，涉及权限问题通过 云函数 进行处理
* 小程序云开发微服务采用 JavaScript 语言编写，无缝衔接
* 云控制台提供的管理工具足够支撑日常数据使用和查看

## 前期工作
### 文件结构变动
* 原生小程序文件结构

```
|-pages	// 页面源码文件夹
|-utils	// 公共方法
|-app.js	// 全局入口js文件
|-app.json	// 全局基础配置
|-app.wxss	// 全局css文件
|-project.config.json	// 项目配置
```
* 云开发小程序文件结构

```
|-cloudfunctions		// 云函数文件夹
|-miniprogram		// 小程序源码
	|-assets	// 静态资源文件
	|-pages	// 页面源码文件夹
	|-utils	// 公共方法
	|-app.js	// 全局入口js文件
	|-app.json	// 全局基础配置
	|-app.wxss	// 全局css文件
|-project.config.json	// 项目配置
```
### 小程序配置信息修改
* `project.config.json` 增加小程序与云函数根目录配置

```
"miniprogramRoot": "miniprogram/",
"cloudfunctionRoot": "cloudfunctions/"
```
* 启用云函数模板模式

`"cloudfunctionTemplateRoot": "cloudfunctionTemplate"`

### 云控制台设置
> 这一部分[官方文档](https://developers.weixin.qq.com/miniprogram/dev/wxcloud/basis/quickstart.html#_1-%E6%96%B0%E5%BB%BA%E4%BA%91%E5%BC%80%E5%8F%91%E6%A8%A1%E6%9D%BF)写的很清楚，不再详细讲述

* 开通云开发
* 配置云环境
* 云控制台配置数据库、存储库等

## 云开发改造与开发
### 添加云函数
### 本地调试
### 上传并部署
### 本地使用

## 部署与发布

## 使用与反馈