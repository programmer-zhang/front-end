## 阅读本文您将收获
* Vue 响应式数据原理
* Vue 响应式数据更新流程

### Vue 的响应式怎么做的，简单说
* `init` 时利用 `Object.defineproperty` 监听数据变化
* 利用 `setter` 和 `getter` 进行触发

### Vue 设置响应式数据的完整流程
* `init` 时利用 `Object.defineproperty` 监听 `Vue实例` 的响应式数据变化从而实现数据劫持功能
* 当 `reder function` 被渲染时，读取实例中与视图相关的响应式数据，从而触发 `getter` 进行依赖收集
* 收集结束后进行正常的渲染和更新
* 数据变化时，触发数据的 `setter` ，通知依赖收集中的和视图相关的 `watcher`，告知需要重新渲染视图，`watcher` 会再次通过updata更新视图