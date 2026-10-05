# 打造网页版微信(二): 利用 wetool 接入微信

> ⚠️ **重要声明：`WeTool` 已被微信官方认定为外挂，于 2020 年 5 月被永久封禁并正式停止服务（官方不再提供下载与新用户接入）。本文仅作为技术方案分享与历史记录，不提供任何可用性保证，请勿用于实际生产环境或任何商业用途。**

> 上一篇文章我们搞定了输入框 [属性 contenteditable 的用处](./wechat_contenteditable.md)。输入框有了，接下来最关键的问题是：**浏览器怎么连上微信？** 本篇就来聊聊如何通过 `WeTool` 的本地 HTTP 接口把微信接进来。

## 阅读本文您将收获
* 网页版微信为什么需要一个"微信接入层"
* `WeTool` 是什么、提供了哪些能力
* 如何用 `Node` 中间层 + `WeTool` 完成登录、拉取联系人、收发消息

## 为什么需要一个接入层
* 浏览器不能直接和微信客户端通信，微信也没有开放给第三方的官方 `Web` 接口
* 所以要找一个**能操作微信 PC 端**的工具，把它的能力以 `HTTP` 接口的形式暴露出来
* 这个工具就是本篇的主角 —— `WeTool`

## WeTool 是什么
* `WeTool` 是一款运行在 **Windows 微信 PC 端**上的第三方增强工具
* 它通过注入/钩子的方式，拿到微信客户端的内部能力，并对外提供一套 **本地 HTTP 接口**（默认监听本机某个端口）
* 常见能力
	* 获取登录二维码
	* 检查登录状态、获取登录用户信息
	* 获取联系人、群聊列表
	* 发送文本 / 图片 / 文件消息
	* 接收消息（通过回调地址或轮询）
	* 群管理、自动回复等（本项目用不到）

> **重要前提**：`WeTool` 只能安装在 **Windows** 上，因为它依赖 Windows 版微信客户端。如果你的开发机是 Mac，需要一台 Windows 机器（或虚拟机 / 云主机）作为"微信宿主"。

## 整体链路
* 微信客户端（Windows）
* ↑ 钩子注入
* `WeTool`（提供 HTTP 接口）
* ↑ HTTP
* `Node 中间层`（本项目 `server/`）
* ↑ HTTP + WebSocket
* 浏览器（`WeHub` 前端）

* 为什么不直接让浏览器请求 `WeTool`？
	* **跨域**：浏览器直连 `WeTool` 会被同源策略拦住
	* **安全**：`WeTool` 的 `token` 不应该暴露在前端
	* **协议差异**：`WeTool` 是"回调式"的，需要一个常驻服务来接回调，再转成 `WebSocket` 推给前端

## 中间层：封装 WeTool 接口
* 在 `Node` 中间层里，把 `WeTool` 的接口统一封装一遍，前端只调自己的接口

```
// server/wetool.js
const axios = require('axios')

const BASE_URL = 'http://127.0.0.1:8899' // WeTool 本地接口地址
const TOKEN = process.env.WETOOL_TOKEN  // 从环境变量拿，绝不写死在代码里

const wc = axios.create({
    baseURL: BASE_URL,
    timeout: 10000,
    headers: { 'Authorization': TOKEN }
})

// 获取登录二维码
exports.getQrcode = () => wc.get('/login/qrcode')

// 检查登录状态
exports.checkLogin = () => wc.get('/login/check')

// 获取联系人列表
exports.getContacts = () => wc.get('/contact/list')

// 发送文本消息
exports.sendText = (wxid, content) =>
    wc.post('/msg/sendText', { wxid, content })

// 发送图片消息（base64 或本地路径）
exports.sendImage = (wxid, base64) =>
    wc.post('/msg/sendImage', { wxid, base64 })
```

## 登录流程
* 第一步：拉二维码给前端展示

```
// server/index.js
app.get('/api/qrcode', async (req, res) => {
    const { data } = await wetool.getQrcode()
    // data 里一般是一个二维码图片的 base64 / url
    res.json({ qrcode: data.qrcode })
})
```

* 第二步：前端轮询登录状态（也可以用 `WebSocket` 推送，这里为了简单用轮询）

```
// 前端 Login.vue
async pollingLogin() {
    this.timer = setInterval(async () => {
        const { status, user } = await api.checkLogin()
        if (status === 'success') {
            clearInterval(this.timer)
            // 登录成功，写入 VueX（见系列第三篇）
            this.$store.dispatch('user/setUser', user)
            this.$router.replace('/home')
        }
    }, 2000)
}
```

* 第三步：登录成功后，初始化数据（联系人、会话、历史消息）并建立 `WebSocket` 连接

## 接收消息
* `WeTool` 收到微信消息后，一般支持两种回传方式
	* **回调模式**：`WeTool` 主动 `POST` 到你配置的回调地址
	* **轮询模式**：你定时去拉取未读消息
* 本项目选用回调模式，在 `Node` 中间层开一个回调接口

```
// server/index.js —— WeTool 回调入口
app.post('/api/wetool/callback', (req, res) => {
    const msg = req.body        // { type, wxid, from, content, timestamp }
    // 把消息通过 WebSocket 推给浏览器
    wsBroadcast({ type: 'newMessage', data: msg })
    res.json({ code: 0 })       // 必须尽快返回，避免 WeTool 重试
})
```

> 回调接口一定要**快速返回**，业务处理放到后面异步做，否则 `WeTool` 会认为你没收到而重复推送。

* 前端收到 `WebSocket` 消息后，交给 `VueX` 处理（系列第三、四篇会详细讲）

```
// 前端接收
websocketonmessage(e) {
    const res = JSON.parse(e.data)
    if (res.type === 'newMessage') {
        this.$store.commit('chat/addMessage', res.data)
    }
}
```

## 踩坑记录
* **端口冲突**：`WeTool` 默认端口如果被占用，接口会莫名其妙失败，先确认它是否真的起来了
* **token 失效**：`WeTool` 重启后 `token` 可能变化，中间层要做好失败重试与状态提示
* **消息乱序 / 重复**：回调 + 轮询混用时容易重复，前端需要对 `msgId` 做去重
* **编码问题**：Windows 上的路径与中文需要统一 `UTF-8`，否则图片 / 文件名会乱码
* **稳定性**：`WeTool` 和服务都不保证 7×24 稳定，前端要有"连接断开 → 重连"的兜底（第四篇）

## 合规与风险提示
* 使用第三方工具接入微信**违反微信用户协议**，存在明确的**封号风险**
* `WeTool` 曾经被腾讯官方重点打击过，请务必**用小号测试**，不要拿主号冒险
* 很多 `WeTool` 下载站夹带木马，请从可信来源获取，并做好杀毒
* 本项目所有相关代码**仅用于前端技术学习**，请勿用于营销外挂、批量加人等违规用途

## 写在最后
* 接入层是整个项目里"最不前端"的一环，但它是所有功能的地基
* 把链路理清楚：**微信 → WeTool → Node → WebSocket → Vue**，后面就都是纯前端的事了
* 下一篇我们来看前端如何组织这些海量数据：[打造网页版微信(三): 利用 VueX 存储数据](./wechat_vuex.md)
