> 近期新上线项目，用到了微信授权获取用户信息和分享，掉坑无数次，遂写此篇，为后人指路

## 项目情况
### 技术选型
* 项目语言：HTML、CSS、JavaScript
* 项目框架：Vue.js
* 项目搭建脚手架：vue-cli
* 工程化工具：webpack、Npm
* 源码管理：gitlab
* 运行环境：WeChatBrowser
* 第三方服务：微信JS-SDK

### 项目需求
* 微信授权获取用户信息
* 微信分享统计
* 提交表单携带微信部分信息

## 微信授权(基于公众号的授权方案)
* 目前网上大多分为两种方式去获取微信授权，一种是前端主导的微信授权，一种是server端主导的微信授权，两种方式实现的结果是一样的，具体采用何种方式可以根据自己项目情况去选择

### 授权方法
* 客户端中转的授权方式
![授权流程](../images/wechat-auth.jpeg)
* 完全由服务端主导的授权方式

### 授权流程
* 客户端中转的授权方式
	* 微信用户进入页面(动态网址需要提前向服务器端获取授权地址)
	* 客户端携带`redirect_uri`向微信服务器发起授权请求
	* 微信服务器授权成功会携带一个`code`在`url`上返回
	* 客户端随即携带`code`向服务端发送请求
	* 服务端返回用户信息
* 完全由服务端主导的授权方式


## 部分参考文献(排名不分先后)
* [网站应用微信登录开发指南](https://open.weixin.qq.com/cgi-bin/showdocument?action=dir_list&t=resource/res_list&verify=1&id=open1419316505&token=&lang=zh_CN)
* [vue 前后端分离 如何实现微信后端授权](https://www.v2ex.com/t/420936)
* [微信授权、获取用户openid](http://www.cnblogs.com/jinzhenzong/p/9035809.html)
* [Vue微信公众号开发踩坑记录](https://segmentfault.com/a/1190000010753247)
* [Vue-mall](https://github.com/qutz/vue-mall)
* [微信登录，前端怎么获取用户的信息](https://segmentfault.com/q/1010000012401356/)