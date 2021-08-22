# VUE 的生命周期

## 阅读本文您将收获
* Vue 的生命周期

## Vue 生命周期流程图(图片来自网络，侵联删)

![](../images/vue-life-cycle.jpeg)

## Vue 生命周期流程
1. 创建实例，`new Vue()` 的过程中，首先执行 `init()`

2. `init()` 过程首先是执行 `beforeCreate` ，初始化`data`、 `props`、 `watch`、`computed`,这些执行都是在 `beforeCreate` 阶段和 `create` 阶段，也是创建响应式数据的阶段，这个阶段不要去修改数据

3. `create` 阶段结束，会去判断实例中有无 `el option` 选项，如果没有会执行 `$mount()`, 如果有，直接执行下一步

4. 判断 `template`, 若有，会把 `template` 打成一个个 `render function` ,其中的传参h就是`vue.createElement`， 参数为 标签，对象(可以是props或事件)，内容

5. `render`函数发生在 `beforemounted` 和 `mounted` 之间，所以当 `beforeMount` 时，`$el` 还只是HTML上的节点，`mounted` 时才把渲染的内容挂载到 `DOM` 上，实际就是执行了 `renderfunction`

6. `beforeMount` 有了 `renderfunction` 才执行，执行完执行 `mount` , `mounted` 执行完，整个生命周期中主动执行的函数就已经完毕，剩下的比如 `beforeUpdata`、`updata`、`beforDestory`、`destory` 需要外部触发