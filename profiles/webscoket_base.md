# webscoket基础

## 阅读本文您将收获
* webscoket 基础介绍
* webscoket 使用指南

## 实时推送技术
* 实时推送技术的实现大体有两种方式，`Ajax轮询` 和 `WebSocket`
	* `Ajax轮询` 是通过设定特定的时间间隔(如：1s), 定时向服务器发送HTTP请求，从而获取数据，达到实时推送的效果。这种传统的形式有明显的缺点，就是客户端需要不断地向服务端发送请求，增加服务器压力
	* `WebSocket` 协议，能更好的节省服务器资源和带宽，并且能够更实时地进行通讯

<center>
    <img src="../images/ws.png">
    <div><span style="color: #aaa; border-bottom: 1px solid #aaa;">ws握手与数据传输  图片来源：菜鸟教程</span></div>
</center>

## 简单介绍
### HTML5 标准
* `HTML5` 新增的协议

### 单个 TCP 连接
* 在单个 TCP 连接上进行全双工（能够同一时候发送和接收）通讯的协议

### 基于HTTP进行链接
* `Webscoket` 并不是全新的协议，而是利用了 `HTTP` 协议来建立连接(完成握手)
* 一次握手，永久连接，双向数据传输
* 通过查看浏览器的request这是一个以 `HTTP` 协议为基础的 get 请求，握手只会进行一次，随后便可以进行双向数据传递

### 不存在跨域的问题
* `webscoket` 协议本身不要求同源策略，不存在跨域的问题，本身就跨域。
* 但是，浏览器会发送 `Origin` 的 `HTTP头` 给服务器，服务器可以根据 `Origin` 拒绝这个 `WebSocket` 请求。所以，是否要求同源要看服务器端如何检查。

### 统一资源标识符
* `ws` 和 `wss`
* 按照HTML5标准是有对应关系的 `HTTP -> ws; HTTPS -> wss`

### 心跳检测
* 链接时间长无数据来往会自动断线，浏览器不会为你维持连接，需要增加心跳检测来保持通道畅通

### 断线重连
* 由于网络原因或服务器不稳定造成的 `webscoket` 断线，需要进行重连

## 属性(已创建webscoket实例的情况下)
* `Socket.readyState`: 只读属性 `readyState` 表示连接状态
	* 0 - 表示连接尚未建立。
	* 1 - 表示连接已建立，可以进行通信。
	* 2 - 表示连接正在进行关闭。
	* 3 - 表示连接已经关闭或者连接不能打开。
* `Socket.bufferedAmount`: 只读属性 `bufferedAmount` 已被 `send()` 放入正在队列中等待传输，但是还没有发出的 UTF-8 文本字节数，也可根据此属性进行判断 webscoket 数据是否发送完成 。

## 方法(已创建webscoket实例的情况下)
* `Socket.onopen`: 连接建立时触发
* `Scoket.onmessage`: 客户端接收服务端数据时触发
* `Scoket.onerror`: 通信发生错误时触发
* `Scoket.onclose`: 连接关闭时触发

## 握手阶段
* 一个握手请求

```
GET /chat HTTP/1.1
Host: server.xxx.com
Connection: Upgrade // 表示要升级协议
Upgrade: websocket	// 升级到 webscoket 协议
Sec-WebSocket-Key: xxxx	 // 提供基本防护，判定是否是恶意或无意的连接
Sec-WebSocket-Protocol: chat, superchat
Sec-WebSocket-Version: 13 // 表示 webscoket 版本，如果服务器不支持此版本，会返回 Sec-WebSocket-Versionheader ，里面包含服务器支持的版本
Origin: http://xxx.com
```
* 相比正常的 HTTP request ，多了两个参数 `Upgrade: websocket` 和 `Connection: Upgrade` ，这两个参数是告诉服务器，我发起的是webscoket请求。

## 使用 Webscoket
### 创建实例
* `var Socket = new WebSocket(url, [protocol]);`
	* 第一个参数 url, 指定连接的 URL
	* 第二个参数 protocol 是可选的，指定了可接受的子协议

### 发送消息
* 链接成功后向服务器 send 消息 (JSON格式)

```
Socket.onopen = function(){
  // Web Socket 已连接上，使用 send() 方法发送数据
  let params = {
  		data: '测试发送数据'
	}
  Socket.send(JSON.stringify(params))
  alert("数据发送中...")
}
```

### 接收消息
* 使用 webscoket 自带方法接收服务器推送消息 (JSON格式)

```
Socket.onmessage = function (e) { 
  let res = JSON.parse(e.data)
  alert("数据已接收...")
}
```

### 关闭连接
* 发生 error 或者主动关闭连接

```
Scoket.onerror = function(){
	// 发生错误
}
Socket.onclose = function(){ 
  // 关闭 websocket
  alert("连接已关闭...")
}
```

## WebScoket 使用注意
### 心跳检测
* WebScoket 在数据传输过程中，会碰到网络断线或者长时间不发送数据而断开的情况，此时 `onclose()` 回调不会触发，这种情况下如果继续发送数据，会出现丢失数据的情况。
* 为了避免这种情况发生，在不借助第三方 WebScoket JS 的情况下，我们要自己进行心跳检测，避免出现掉线而持续发送数据的情况。

### 断线重连
* 断线重连是为了当 WebScoket 断开时，可以自己进行重新连接请求，重新恢复客户端和服务端的数据传输。