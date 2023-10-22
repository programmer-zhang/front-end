# 一个 WebScoket 实例

> 上篇文章为大家讲解了 WebScoket 的基础，本篇文章就为大家通过一个实例介绍 WebScoket 的使用。没有看基础介绍的同学可以点击后边的链接查看。[WebScoket 基础介绍](https://github.com/programmer-zhang/front-end/tree/master/profiles/webscoket_base.md)

> 本实例基于一个 Vue-cli 项目

## 创建思路
### 工厂函数抽离
* 将 WebScoket 进行封装，以进行实例化，方便使用

### 同Vue实例下需要创建多个 WebScoket 
* 利用 ES6 class 创建
* path 可自定义传入，解决多个 scoket 连接同时创建与存在

### WebScoket 回调单独处理
* WebScoket handler 通过创建实例后挂载
* 挂载函数自定义

### 心跳检测
* 实例添加公用心跳检测

### Ready State 抽离
* 实例封装 readystate 状态，便于获取

### 断线重连
* 断线重连单独在使用示例时进行处理，便于解决业务需要

## 代码部分
### WebScoket部分
* WebScoket 要有一定的可扩展性和灵活性，通过 node 环境变量的方式注入请求地址。
* 基础介绍中说到的 WebScoket 需要解决的心跳问题，也在实例中增加了心跳检测，其中心跳检测的心跳时间需要开发者自己去掌握，在不影响基本数据传递的基础上，选择合适的心跳时间。
	* 心跳检测机制就是通过 scoket 链接将心跳包按照心跳检测周期时间发送到服务器端，同时服务器端将回应消息发送回来，以确保连接正常，双方能够正常传递信息。
* 将 WebScoket 的相关属性API整体暴露，便于在业务中进行调用处理。

```
// scoket.js
const webscketurl = process.env.BACKEND_URL // 利用node环境变量设置scoket path

class WebSocketUtil{
	constructor(wspath){
		let protocol = (window.location.protocol == 'https:') ? 'wss://' : 'ws://';
		// 判断当前使用协议头(上一篇 WebScoket 基础已讲过)
		let wsuri = protocol + webscketurl + wspath

		// new 实例
		this.websock = new WebSocket(wsuri)
	}

	// 心跳检测
	heartCheck(){ 
		let self = this;
		if (this.timeoutObj) {
			clearInterval(this.timeoutObj)
		}
    	this.timeoutObj = setInterval(function(){
      		// 这里发送一个心跳，后端收到后，返回一个心跳消息，
      		// onmessage拿到返回的心跳就说明连接正常
      		if (self.readyState() != 1) {
        		clearInterval(self.timeoutObj)
			}
			self.websock.send("HeartBeat");
			// 发送心跳检测
      		console.log("HeartBeat");
		}, 5000)
  	}

  	// 实例创建后自定义创建 handler
  	on(funName,handler){
		this.websock[`on${funName}`] = handler
		// 暴露onmessage、onclose、onopen、onerror方法
	}
 
	// 发送scoket数据
	send(msg){
		this.websock.send(msg)
	}

	// 监听websock链接状态
	readyState(){
	// 获取webscoket实例链接状态 0：正在建立连接连接,还没有完成 1：连接成功,可以进行通信 2：连接正在进行关闭握手,即将关闭 3：连接已经关闭或者根本没有建立
		return this.websock.readyState;
	}
}

// 导出实例
export default WebSocketUtil
```
 
### 实例创建部分

```
// index.vue
import $WebSocketUtil from '../../api/scoket.js'

// 实例化聊天scoket
initScoket() {
	let token = this.$cookies.get('token')
	let pathData = '/chat?token=' + token
	this.webscoketChat = new $WebSocketUtil(pathData)
	this.webscoketChat.on('message', this.websocketonmessage)
	this.webscoketChat.on('open', this.websocketonpen)
	this.webscoketChat.on('error', this.websocketonerror)
	this.webscoketChat.on('close', this.websocketonerror)
	Vue.prototype.$scoketChat = this.webscoketChat
},

// socket建立成功后开始进行心跳检测
websocketonpen() {
	this.webscoketChat.heartCheck()
},

// 发生错误断线重连
websocketonerror() {
	let self = this
	if (this.reconnectChat) { return }
	this.reconnectChat = true
	setTimeout(function () {
	  self.initScoket()
	  console.log("Chat正在重连...")
	  self.reconnectChat = false
	}, 10000)
},

// 接收 webscoket 消息
websocketonmessage(e) {
	let res = JSON.parse(e.data);
	HandleChat(res.type,res.data,res.wxId || '');
},
```

## 写在最后
* 如果你对 WebScoket 一无所知，建议先学习这篇文文章， [WebScoket 基础介绍](https://github.com/programmer-zhang/front-end/tree/master/profiles/webscoket_base.md)
* 本实例仅是根据自身需求和代码逻辑编写，并不一定适合所有项目，需要根据实际情况进行改进，如果您有更好的想法，欢迎交流 chinajnzhang@hotmail.com