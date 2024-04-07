# 谷歌浏览器扩展程序制作指南

> 出于对隐私保护、安全性、性能的加强，谷歌随着 `Chrome 88` 的升级，将推行 `Manifest V3` 扩展程序规范。

> 本文也将基于 `Manifest V3` 规范进行扩展程序制作。

> 本文使用 `ChatGPT` 辅助编写

## 目录结构
* 以下为 `Chrome` 官方文档给出的参考目录结构
* 可以通过多种方式构建项目目录，唯一的前提条件是将 `manifest.json` 文件放在根目录中



## Manifest 配置
* `manifest.json` 文件的配置详情如下:

```
{
	"name": "rep-tool",
	"version": "0.0.1",
	"manifest_version": 3,
	// 简单描述
	"description": "a chrome browser extension tool",
	// 插件图标
	"icons": {
		"16": "./logo.jpeg",
		"48": "./logo.jpeg",
		"128": "./logo.jpeg"
	},
	// 选择默认语言
	"default_locale": "en",
	
	// 浏览器图标部分
	"action": {
	    "default_title": "小助手",
	    "default_icon": "./logo.jpeg",
	    "default_popup": "./popup.html"
	},
	
	// 后台常驻文件
	"background": {
		"service_worker": "service-worker.js"
	},

	// 引入一个脚本
	"content_scripts": [
		{
			"matches": ["<all_urls>"],
			"js": ["qrcode.min.js", "jquery.min.js"]
		}
	],
	"web_accessible_resources": [
		{
			"matches": ["<all_urls>"],
			"resources": ["qrcode.min.js", "jquery.min.js"]
		}
	],
	// 应用权限配置:cookie 权限，系统通知权限等
	"permissions": [
		"contextMenus",
		"tabs",
		"storage",
		"cookies",
		"notifications",
		"alarms"
	  ]
}

```

### 权限类型

> 在 V3 版本当中可以声明以下类别的权限

* `permissions` : 包含写明的权限列表中的项
* `optional_permissions ` : 用户运行时授予的权限
* `content_scripts.matches` :  包含一个或多个匹配模式，可允许内容脚本注入一个或多个主机中
* `host_permissions`: 包含一个或多个匹配模式，可提供对一个或多个主机的访问权限，多用于 `fetch()` 跨域
* `optional_host_permissions`: 由用户在运行时授予

## 主体页面



## 运行插件

## 错误排查
### `onMessage` 监听器消息端口自动关闭
>  错误信息：`Unchecked runtime.lastError: The message port closed before a response was received.`

* 可能错误原因一： `chrome.runtime.onMessage` 监听器在异步操作完成之前关闭了消息端口，导致无法发送响应。

* 解决方案一: 保持消息端口打开，使用 return true 提前返回或者二次监听的方法
https://gitcode.csdn.net/65e95d911a836825ed790a29.html?dp_token=eyJ0eXAiOiJKV1QiLCJhbGciOiJIUzI1NiJ9.eyJpZCI6NTE3NDg1MSwiZXhwIjoxNzExMzc3NjQ5LCJpYXQiOjE3MTA3NzI4NDksInVzZXJuYW1lIjoidHVmZWlfemhhbmcifQ.TlQuK0xO3uZzlcLs313sgEpqQI_id7CngV4CWIqc5WQ

早期的issue: https://github.com/mozilla/webextension-polyfill/issues/130

* 可能错误原因二：`chrome.runtime.sendMessage` 未正确使用回调
https://blog.csdn.net/m0_37729058/article/details/89186257

* 可能错误原因三：跨域权限
`host_permissions` 权限配置

### 使用 `fetch` 发送数据后接收不到 `response`

* 原因一: `response` 数据需要`JSON` 格式化数据后 `return`

```
fetch(request.url + request.body).then(response => {
        return response.json(); // 必不可少
}).then(json => {
        sendResponse({ success: true, json }); // 真实处理数据
}).catch(err => {
        sendResponse({ success: false, error: 'Failed to send the request' });
})
```