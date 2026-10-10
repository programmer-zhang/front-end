# 打造网页版微信(四): 封装 WebScoket 进行网络消息传输

> 前三篇我们把输入框、微信接入、数据存储都搞定了。剩下最后一环：**消息怎么实时进来、怎么可靠发出去？** 本篇把原生 `WebSocket` 封装成一个能复用的网络层。

> `WebSocket` 的基础概念之前单独写过，不熟悉的同学建议先看：[WebScoket 基础介绍](https://github.com/programmer-zhang/front-end/tree/master/profiles/webscoket_base.md)。这里不再重复握手、`readyState` 等基础内容。

## 阅读本文您将收获
* 为什么要封装 `WebSocket`，而不是直接用原生 API
* 一个可复用的 `WebSocket` 类：心跳、重连、消息队列、事件订阅
* 消息协议设计，以及如何对接 `VueX`

## 为什么不直接用原生 WebSocket
* 原生 `WebSocket` 有几个"裸奔"的问题
	* 断线后不会自动重连，用户以为在聊，实际消息全丢了
	* 长时间无数据来往会被中间层（网关 / 代理）静默断开，`onclose` 都不触发
	* 每个业务都要自己写 `onmessage` 分发，代码重复且容易漏
	* 网络抖动时发送失败没有兜底
* 所以我们把它封装成一个类，对外只暴露 `connect` / `send` / `on` / `close`，这也是软件开发中的常用方式

## 设计目标
* **单例连接**：整个应用共用一个 `WebSocket`，避免多开浪费资源
* **自动重连**：断开后按退避策略重连，重连成功通知业务做数据补偿
* **心跳保活**：定时发送心跳包，及时发现"假连接"
* **消息队列**：连接未就绪时，发送的消息先入队，连上后补发
* **事件订阅**：业务通过 `on(type, handler)` 订阅自己关心的消息类型

## 消息协议设计
* 前后端约定一个统一的报文结构，所有消息都长这样

```
{
    "type": "newMessage",     // 消息类型，用于事件分发
    "data": { ... },          // 业务数据
    "msgId": "xxx",           // 消息唯一 id，用于 ack 与去重
    "timestamp": 16999999999  // 时间戳
}
```

* 常见的 `type` 约定

类型|方向|含义
:--:|:--:|:--
`newMessage`|服务端 → 客户端|收到新消息
`messageAck`|服务端 → 客户端|消息发送确认
`contactUpdate`|服务端 → 客户端|联系人/会话变更
`online`|服务端 → 客户端|连接建立成功
`heartbeat`|双向|心跳

## 封装实现

```
// api/scoket.js
const WS_URL = process.env.WS_BACKEND_URL   // 利用 node 环境变量注入地址

class WebSocketClient {
    constructor() {
        this.ws = null
        this.url = WS_URL
        this.handlers = {}          // { type: [handler, ...] }
        this.queue = []             // 未连接时缓存待发消息
        this.reconnectCount = 0
        this.maxReconnect = 5
        this.heartbeatTimer = null
        this.isManualClose = false  // 手动关闭时不触发重连
    }

    // 建立连接
    connect() {
        if (this.ws && this.ws.readyState === 1) return

        this.ws = new WebSocket(this.url)

        this.ws.onopen = () => {
            console.log('[ws] connected')
            this.reconnectCount = 0
            this.startHeartbeat()
            this.flushQueue()          // 补发缓存消息
            this.emit('online', null)  // 通知业务
        }

        this.ws.onmessage = (e) => {
            let res
            try {
                res = JSON.parse(e.data)
            } catch (err) {
                console.warn('[ws] 非法报文', e.data)
                return
            }
            // 心跳响应不往下分发
            if (res.type === 'heartbeat') return
            this.emit(res.type, res.data)
        }

        this.ws.onclose = () => {
            console.log('[ws] closed')
            this.stopHeartbeat()
            if (!this.isManualClose) this.reconnect()
        }

        this.ws.onerror = (err) => {
            console.error('[ws] error', err)
            // onerror 后通常会紧跟着 onclose，重连逻辑统一放 onclose
        }
    }

    // 事件订阅
    on(type, handler) {
        if (!this.handlers[type]) this.handlers[type] = []
        this.handlers[type].push(handler)
    }

    off(type, handler) {
        const list = this.handlers[type]
        if (!list) return
        this.handlers[type] = list.filter(h => h !== handler)
    }

    // 事件分发
    emit(type, data) {
        (this.handlers[type] || []).forEach(handler => {
            try {
                handler(data)
            } catch (e) {
                console.error(`[ws] handler error @${type}`, e)
            }
        })
    }

    // 发送消息（未连接则入队）
    send(type, data) {
        const msg = JSON.stringify({ type, data, msgId: this.genId() })
        if (this.ws && this.ws.readyState === 1) {
            this.ws.send(msg)
        } else {
            this.queue.push(msg)
        }
    }

    // 连接就绪后补发队列
    flushQueue() {
        while (this.queue.length) {
            this.ws.send(this.queue.shift())
        }
    }

    // 心跳保活
    startHeartbeat() {
        this.stopHeartbeat()
        this.heartbeatTimer = setInterval(() => {
            // readyState !== 1 说明连接已异常，直接重连
            if (this.ws.readyState !== 1) {
                this.ws.close()
                return
            }
            this.send('heartbeat', { ts: Date.now() })
        }, 15000)   // 心跳间隔要小于服务端/网关的空闲超时时间
    }

    stopHeartbeat() {
        if (this.heartbeatTimer) {
            clearInterval(this.heartbeatTimer)
            this.heartbeatTimer = null
        }
    }

    // 断线重连
    reconnect() {
        if (this.reconnectCount >= this.maxReconnect) {
            this.emit('reconnectFail', null)   // 通知业务，让用户手动刷新
            return
        }
        this.reconnectCount++
        // 1s, 2s, 4s, 8s, 16s ... 最多 30s，避免把服务端打爆
        const delay = Math.min(1000 * Math.pow(2, this.reconnectCount - 1), 30000)
        console.log(`[ws] 第 ${this.reconnectCount} 次重连，${delay}ms 后...`)
        setTimeout(() => this.connect(), delay)
    }

    // 主动关闭
    close() {
        this.isManualClose = true
        this.stopHeartbeat()
        if (this.ws) this.ws.close()
    }

    genId() {
        return Date.now().toString(36) + Math.random().toString(36).slice(2, 8)
    }
}

// 全局单例
export default new WebSocketClient()
```

## 为什么用指数退避
* 断线往往是**服务端挂了或网络断了**，如果固定 1s 重连，几十个客户端同时猛冲，会把刚恢复的服务端再次打垮
* 指数退避 + 上限，既能尽快恢复，又不会造成"重连风暴"
* 超过最大次数后，通知业务层"重连失败"，让用户看到明确提示，而不是傻等

## 心跳间隔怎么定
* 网关 / 反向代理（如 `Nginx`）通常有 `proxy_read_timeout`，默认 60s
* 心跳间隔要**明显小于这个值**，否则会被代理判定为空闲连接而断开
* 本项目取 **15s**，兼顾及时性和流量

## 对接 VueX
* 网络层只负责"收和发"，业务逻辑交给 `VueX` 的 `action`
* 在应用初始化时，把 socket 事件订阅到 `store`

```
// main.js 或 Home.vue 初始化
import socket from './api/scoket'

socket.on('online', () => {
    // 重连成功后，拉一次增量，补偿断线期间丢失的消息
    store.dispatch('chat/syncOfflineMessages')
})

socket.on('newMessage', (msg) => {
    store.commit('chat/ADD_MESSAGE', msg)
})

socket.on('messageAck', ({ msgId }) => {
    store.commit('chat/SET_MSG_STATUS', { id: msgId, status: 'sent' })
})

socket.on('reconnectFail', () => {
    store.commit('user/SET_OFFLINE', true)
})

// 建立连接
socket.connect()
```

```
// 发送时直接用单例
actions: {
    sendMessage({ commit }, { sessionId, content }) {
        socket.send('sendMessage', { sessionId, content })
    }
}
```

## 踩坑记录
* **不要在 `onerror` 里重连**：`onerror` 后一般会触发 `onclose`，在 `onclose` 里统一重连即可，否则会重连两次
* **手动关闭要标记**：用户登出 / 切换账号时 `close()`，要设 `isManualClose`，否则会被自动重连
* **页面卸载记得关**：`beforeDestroy` 里 `socket.close()`，避免内存泄漏和幽灵连接
* **消息去重**：重连后服务端可能重推，前端按 `msgId` 去重（这也是协议里带 `msgId` 的原因）
* **多标签页**：多个标签页会各建一条 `WebSocket`，如果只需要一条，用 `BroadcastChannel` / `SharedWorker` 做单例

## 写在最后
* 网络层是整个应用的"最后一公里"，封好了，业务代码就只剩"订阅事件 + 改 `VueX`"
* 核心就四件事：**保活、重连、补发、分发**
* 至此，`WeHub` 系列四篇完结。回头再看这条链路：
	* 输入框（第一篇）→ 微信接入（第二篇）→ 数据存储（第三篇）→ 网络传输（第四篇）
* 如果你对 `WebSocket` 的结构还不太熟，建议配合基础篇一起看：[WebScoket 基础介绍](https://github.com/programmer-zhang/front-end/tree/master/profiles/webscoket_base.md) / [WebScoket 实例](https://github.com/programmer-zhang/front-end/tree/master/profiles/webscoket_example.md)
