# Vue 源码系列 - this.$nextTick

> 本文使用Grok辅助编写

## 阅读本文您将收获
* Vue 为什么要搞一个异步更新队列
* Vue2 与 Vue3 中 `nextTick` 的实现差异
* `$nextTick` 的常见使用场景和踩坑点

## 从一个必现的坑说起

```
<div ref="box">{{ msg }}</div>

mounted() {
  this.msg = 'hello'
  console.log(this.$refs.box.innerText) // 拿到的还是旧值
}
```

改了数据，紧接着读 DOM，读到的还是老的。不是 `ref` 坏了，是 Vue 根本还没开始更新 DOM。

## 结论先摆出来

* 修改响应式数据，只是通知 `watcher` 入队，**不会立刻更新 DOM**
* 同一个事件循环（tick）内多次修改，`watcher` 队列会去重，最后**只渲染一次**
* 真正刷新 DOM 的动作被放进了微任务里，`$nextTick` 就是等这个动作做完之后执行的回调

所以 `$nextTick` 本质是：**把回调塞进当前这轮更新队列的末尾，等 DOM 更新完再执行**。

## Vue2 的实现

核心文件只有一个：`src/core/util/next-tick.js`，里面就三个东西——`callbacks` 数组、`pending` 标志、`timerFunc`。

### 1. 用什么样的异步方式

`timerFunc` 是降级选出来的：

* `Promise.then`（首选，微任务）
* `MutationObserver`（不支持 Promise 时，仍然是微任务）
* `setImmediate`（再不行，宏任务）
* `setTimeout(fn, 0)`（兜底，宏任务）

```
let timerFunc

if (typeof Promise !== 'undefined' && isNative(Promise)) {
  const p = Promise.resolve()
  timerFunc = () => {
    p.then(flushCallbacks)
    // iOS 的 UIWebView / 微信内置浏览器里微任务队列可能不会主动刷新，在这里塞一个空宏任务兜底
    if (isIOS) setTimeout(noop)
  }
  isUsingMicroTask = true
} else if (!isIE && typeof MutationObserver !== 'undefined' && (isNative(MutationObserver) || MutationObserver.toString() === '[object MutationObserverConstructor]')) {
  let counter = 1
  const observer = new MutationObserver(flushCallbacks)
  const textNode = document.createTextNode(String(counter))
  observer.observe(textNode, { characterData: true })
  timerFunc = () => {
    counter = (counter + 1) % 2
    textNode.data = String(counter)
  }
  isUsingMicroTask = true
} else if (typeof setImmediate !== 'undefined' && isNative(setImmediate)) {
  timerFunc = () => { setImmediate(flushCallbacks) }
} else {
  timerFunc = () => { setTimeout(flushCallbacks, 0) }
}
```

优先微任务的原因很简单：微任务在当前宏任务结束后立刻执行，用户几乎感知不到延迟；而且 `$nextTick` 的回调如果落在宏任务里，会排在所有微任务后面，时序会变得不可控。

### 2. nextTick 本体

```
const callbacks = []
let pending = false

export function nextTick (cb, ctx) {
  let _resolve
  callbacks.push(() => {
    if (cb) {
      try {
        cb.call(ctx)
      } catch (e) {
        handleError(e, ctx, 'nextTick')
      }
    } else if (_resolve) {
      _resolve(ctx)
    }
  })

  if (!pending) {
    pending = true
    timerFunc()
  }

  // 不传回调时返回 Promise（2.1.0+）
  if (!cb && typeof Promise !== 'undefined') {
    return new Promise(resolve => { _resolve = resolve })
  }
}
```

几个细节值得注意：

* `pending` 保证**一个 tick 内只调度一次** `timerFunc`，后面进来的回调只是往 `callbacks` 里 push
* 回调用 `cb.call(ctx)` 执行，`ctx` 就是组件实例，所以 `this.$nextTick` 里的 `this` 指向当前组件
* 每个回调单独 `try/catch`，一个回调抛错不会影响后面的回调执行
* 不传 cb 时返回 Promise，所以 `await this.$nextTick()` 也能用

`this.$nextTick` 本身只是个转发，挂在原型上：

```
Vue.prototype.$nextTick = function (fn) {
  return nextTick(fn, this)
}
```

### 3. flushCallbacks

```
function flushCallbacks () {
  pending = false
  const copies = callbacks.slice(0)
  callbacks.length = 0
  for (let i = 0; i < copies.length; i++) {
    copies[i]()
  }
}
```

先 `slice` 一份副本、再清空原数组，这是为了防止回调里又调用 `nextTick` 导致死循环——新进来的回调会被推到下一轮。

### 4. 最关键的一步：渲染 watcher 也走 nextTick

如果只看到上面，会有一个疑问：渲染 watcher 的 flush 和用户的 `$nextTick` 回调，谁先谁后？

看 `queueWatcher`：

```
export function queueWatcher (watcher: Watcher) {
  const id = watcher.id
  if (has[id] == null) {
    has[id] = true
    if (!flushing) {
      queue.push(watcher)
    } else {
      // 正在 flush 中，按 id 大小插到合适位置，保证父子组件顺序
      let i = queue.length - 1
      while (i > index && queue[i].id > watcher.id) {
        i--
      }
      queue.splice(i + 1, 0, watcher)
    }
    if (!waiting) {
      waiting = true
      nextTick(flushSchedulerQueue)
    }
  }
}
```

答案就在这里：**`flushSchedulerQueue` 本身就是通过 `nextTick` 注册的第一个回调**。

所以整个时序是：

1. 改数据 → `dep.notify()` → `queueWatcher` → `nextTick(flushSchedulerQueue)`，`pending = true`，`timerFunc()` 开始排微任务
2. 业务代码里紧接着写 `this.$nextTick(cb)`，`cb` 被 push 到 `callbacks` 的第二位
3. 微任务触发 → `flushCallbacks` → 先执行 `flushSchedulerQueue`（这里才真正执行渲染 watcher，更新 DOM），再执行用户的 `cb`

这就是 `$nextTick` 一定能拿到最新 DOM 的根本原因：**不是靠猜时序，而是排在同一个回调队列里、注册得更早**。

## Vue3 的实现

Vue3 把异步调度收进了 `scheduler.ts`，不再维护 callbacks 数组，直接复用 Promise 链：

```
const resolvedPromise = Promise.resolve()
let currentFlushPromise = null

function queueFlush() {
  if (!isFlushing && !isFlushPending) {
    isFlushPending = true
    currentFlushPromise = resolvedPromise.then(flushJobs)
  }
}

export function nextTick(fn) {
  const p = currentFlushPromise || resolvedPromise
  return fn ? p.then(fn) : p
}
```

* 没有队列了，`nextTick` 直接 `then` 在 `currentFlushPromise` 后面
* 没有 flush 任务在排队时（`currentFlushPromise` 为 `null`），就 `then` 在 `resolvedPromise` 上，退化成"下一个微任务"
* 因为用的是同一条 Promise 链，`await nextTick()` 之后 DOM 一定是最新的

顺带一个差异：Vue3 的 `nextTick` 是独立导出的函数，`this.$nextTick` 依然存在，但组件里更推荐 `import { nextTick } from 'vue'`。

## 常见使用场景

* 改完数据，立刻要量 DOM 尺寸、滚动高度、`scrollTop`
* `v-if` 切换后要拿子组件实例或 `ref` 节点（`v-show` 不用等，节点一直在）
* 图表库（ECharts、G2）初始化时容器还是 `display: none`，需要等容器可见后再 `init`
* 父组件改完传给子组件的 `props`，等子组件更新完再做后续动作
* 想避开 `updated` 里改数据引发的二次更新，用 `$nextTick` 把逻辑挪到下一轮

## 踩坑点

* **`created` 里拿不到 DOM**，跟 `$nextTick` 无关，这时候还没 `$mount`，该写 `mounted`
* **`$nextTick` 不能保证"数据一定渲染完"**：如果改数据发生在更晚的异步回调里（比如 `await` 之后的另一个微任务），当前这次 `$nextTick` 不包含它
* **不要在 `$nextTick` 回调里无限改数据**，会形成持续的更新循环
* **Vue2 降级到 `setTimeout` 时，`$nextTick` 会晚于用户自己的 `Promise.then`**，不要假设它一定是最快的微任务
* **多个 `$nextTick` 按注册顺序执行**，同一 tick 内只调度一次 `timerFunc`，别指望后注册的先跑

## 小结

Vue 的异步更新是性能设计：合并同一 tick 的多次修改，只渲染一次。`$nextTick` 不是什么黑魔法，只是**和渲染任务共用一个回调队列，并且排得比渲染晚**。

系列其它文章：

* [Vue 源码系列-Vue 的生命周期](./vue_life.md)
* [Vue 源码系列-响应式数据](./vue_响应式流程.md)
* [Vue 源码系列-watch](./vue_watch.md)
* [Vue 源码系列-computed](./vue_computed.md)
