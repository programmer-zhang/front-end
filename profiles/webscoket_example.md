# 一个 webscoket 实例

> 本实例基于一个 Vue-cli 项目

## 创建思路
### 工厂函数抽离
* 将 webscoket 进行封装，以进行实例化

### 同Vue实例下需要创建多个 webscoket 
* 利用 ES6 class 创建
* path 可自定义传入

### webscoket 回调单独处理
* webscoket handler 通过创建实例后挂载
* 挂载函数自定义

### 心跳检测
* 实例添加公用心跳检测

### Ready State 抽离
* 实例封装 readystate 状态，便于获取

### 断线重连
* 断线重连单独在使用示例时进行处理

## 代码部分
### webSocket部分

```
// scoket.js
const webscketurl = process.env.BACKEND_URL // 利用node环境变量设置scoket path

class WebSocketUtil{
  constructor(wspath){
    let protocol = (window.location.protocol == 'https:') ? 'wss://' : 'ws://';
    // 判断当前使用协议头(上一篇webscoket基础已讲过)
    let wsuri = protocol + webscketurl + wspath
    
    this.websock = new WebSocket(wsuri)
    // new 实例
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
    }, 10000)
  }
  
  // 实例创建后自定义创建 handler
  on(funName,handler){
      this.websock[`on${funName}`] = handler
    // 封装onmessage、onclose、onopen、onerror方法
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
* 本实例仅是根据自身需求和代码逻辑编写，并不一定适合所有项目，需要根据实际情况进行改进，如果您有更好的想法，欢迎交流 chinajnzhang@hotmail.com