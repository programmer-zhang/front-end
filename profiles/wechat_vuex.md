# 打造网页版微信(三): 利用 VueX 存储数据

> 前两篇解决了输入框和微信接入 [属性 contenteditable 的用处](./wechat_contenteditable.md) / [利用 wetool 接入微信](./wechat_wetool.md)。
> 
> 数据有了，接下来就是前端最麻烦的一环：**会话、消息、联系人这么多数据，放哪、怎么改、怎么保证视图不乱？

## 阅读本文您将收获
* 为什么用 `VueX` 而不是 `props` / `EventBus`
* 聊天类应用 `store` 的模块划分与数据结构设计
* 处理大量消息时的性能与 `$set` 相关的坑

## 为什么用 VueX
* 聊天应用的痛点是**数据跨组件、实时变化**
	* 会话列表要显示未读数，消息列表要显示消息，两者都要响应"新消息"
	* 输入框、消息列表、会话列表可能分属不同层级，靠 `props` 一层层传会疯掉
* `EventBus` 能传数据，但**数据没有单一来源**，调试时根本不知道谁改了它
* `VueX` 提供了 `state`（唯一数据源）+ `mutation`（唯一改法）+ `getter`（派生数据），适合这种场景

## 模块划分
* 按业务域拆成三个 `module`，避免一个 `store` 文件越写越长

```
store/
├─ index.js
└─ modules/
   ├─ user.js       # 登录用户、登录态
   ├─ contacts.js   # 联系人与群
   └─ chat.js       # 会话列表 + 消息
```

```
// store/index.js
import Vue from 'vue'
import Vuex from 'vuex'
import user from './modules/user'
import contacts from './modules/contacts'
import chat from './modules/chat'

Vue.use(Vuex)

export default new Vuex.Store({
    modules: { user, contacts, chat }
})
```

## user 模块：登录态
* 数据结构简单，只存当前登录用户

```
// store/modules/user.js
export default {
    namespaced: true,
    state: {
        info: null,       // { wxid, nickname, avatar }
        isLogin: false
    },
    mutations: {
        SET_USER(state, user) {
            state.info = user
            state.isLogin = !!user
        }
    },
    actions: {
        setUser({ commit }, user) {
            commit('SET_USER', user)
            // 顺手持久化，刷新不丢登录态
            localStorage.setItem('wehub_user', JSON.stringify(user))
        }
    }
}
```

## chat 模块：会话与消息（核心）
* 这是最重的模块，先设计数据结构

```
// store/modules/chat.js
import Vue from 'vue'

export default {
    namespaced: true,
    state: {
        // 会话列表：只存"索引"，真正的消息放到 messages 里
        sessions: [],            // [{ id, name, avatar, lastMsg, unread, top }]
        // 消息字典：以会话 id 为 key，value 是该会话的消息数组
        messages: {},            // { [sessionId]: [msg, msg, ...] }
        // 当前打开的会话
        activeId: ''
    },
    mutations: {
        // 新增一条消息
        ADD_MESSAGE(state, msg) {
            const { sessionId } = msg
            // 关键：messages 里可能还没有这个 key，用 Vue.set 保证响应式
            if (!state.messages[sessionId]) {
                Vue.set(state.messages, sessionId, [])
            }
            state.messages[sessionId].push(msg)

            // 同步更新会话列表的"最后一条消息"与未读数
            const session = state.sessions.find(s => s.id === sessionId)
            if (session) {
                session.lastMsg = msg.content
                if (sessionId !== state.activeId) {
                    session.unread = (session.unread || 0) + 1
                }
            }
        },
        SET_ACTIVE(state, id) {
            state.activeId = id
            // 打开会话，未读清零
            const session = state.sessions.find(s => s.id === id)
            if (session) session.unread = 0
        }
    }
}
```

### 为什么用"字典 + 数组"两级结构
* 如果所有消息都塞在一个大数组里，每次要拿某个会话的消息都要 `filter` 一遍，消息一多就卡
* 用 `messages[sessionId]` 直接定位，时间复杂度从 O(n) 降到 O(1)
* 会话列表只存"轻量索引"，渲染列表时不需要遍历消息内容

## getter：派生数据
* 未读总数、当前会话消息等，用 `getter` 算，不要塞进 `state`

```
getters: {
    // 未读总数（用于标题栏、浏览器 favicon 角标）
    totalUnread: state =>
        state.sessions.reduce((sum, s) => sum + (s.unread || 0), 0),

    // 当前会话的消息列表
    activeMessages: state =>
        state.messages[state.activeId] || []
}
```

```
// 组件里使用
computed: {
    totalUnread() { return this.$store.getters['chat/totalUnread'] },
    messages() { return this.$store.getters['chat/activeMessages'] }
}
```

## 坑一：直接改 state，视图不更新
* `Vue 2` 的响应式是基于 `Object.defineProperty` 的，**动态新增的属性不会触发更新**
* 比如上面 `state.messages[sessionId] = []`，如果 sessionId 是新 key，视图不会动

```
// ❌ 错误：新属性不会触发响应式
state.messages[sessionId] = []

// ✅ 正确：用 Vue.set（组件内则用 this.$set）
Vue.set(state.messages, sessionId, [])
```

> 关于 `$set` 的更多细节，之前单独写过一篇：[Vue this.$set 的正确打开方式](https://github.com/programmer-zhang/front-end/tree/master/profiles/vue_this.set.md)

* 同理，往会话对象上新增字段也要用 `$set`

## 坑二：大量消息导致卡顿
* 一个活跃的群聊动辄几千上万条消息，全渲染必卡
* 几个优化方向
	* **消息分页**：只渲染最近 N 条，向上滚动再加载历史
	* **虚拟列表**：只渲染可视区内的消息（推荐 `vue-virtual-scroller`）
	* **冻结历史消息**：历史消息不会被修改，用 `Object.freeze()` 跳过响应式劫持，省掉大量 `Watcher`

```
// 历史消息只需展示，不需要响应式
const history = Object.freeze(historyList.map(m => Object.freeze(m)))
```

## 坑三：严格模式下的性能
* 开发环境建议开启 `strict: true`，任何"非 mutation 修改 state"都会报错，能帮你抓出隐藏 bug
* 但严格模式会做深度 `watch`，**生产环境务必关掉**，否则大 `state` 下性能会明显下降

```
export default new Vuex.Store({
    strict: process.env.NODE_ENV !== 'production',
    modules: { user, contacts, chat }
})
```

## 持久化
* 聊天记录、登录态希望在刷新后还在，但又不想每次都全量写 `localStorage`
* 方案一：用 `vuex-persistedstate`，配置 `paths` 只持久化需要的部分

```
import createPersistedState from 'vuex-persistedstate'

plugins: [
    createPersistedState({
        key: 'wehub',
        // 只持久化用户信息和会话列表，消息量太大不全存
        paths: ['user.info', 'chat.sessions']
    })
]
```

* 方案二：消息量大时改用 `IndexedDB`（`localforage`），`localStorage` 有 5MB 上限，撑不住聊天记录

## action：串起网络与状态
* `mutation` 只做同步改数据，`action` 负责"发请求 / 发 WebSocket，再 commit"

```
actions: {
    async sendMessage({ commit, dispatch }, { sessionId, content }) {
        // 乐观更新：先把消息渲染出来，再发网络请求，体验更顺滑
        const msg = { id: Date.now(), sessionId, content, self: true, status: 'sending' }
        commit('ADD_MESSAGE', msg)

        try {
            await api.sendText(sessionId, content)
            commit('SET_MSG_STATUS', { id: msg.id, status: 'sent' })
        } catch (e) {
            commit('SET_MSG_STATUS', { id: msg.id, status: 'failed' })
        }
    }
}
```

> **乐观更新**：先本地渲染，再等网络结果，失败时标记重发。这是聊天应用体验的关键。

## 写在最后
* `VueX` 用好了，聊天应用的数据流会非常清晰：`action → mutation → state → view`，单向不可逆
* 记住三条铁律：**数据只有一份、只通过 mutation 改、派生数据用 getter**
* 下一篇我们来看网络层，把 WebSocket 封装成一个通用的工具：[打造网页版微信(四): 封装 WebScoket 进行网络消息传输](./wechat_webscoket.md)
