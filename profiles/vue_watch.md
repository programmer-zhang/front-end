## 阅读本文您将收获
* `Vue watch` 的流程

### Vue watch 流程
1. 创建实例时会去处理`watch`，这点在前面生命周期中已经提到
2. 遍历数据keys去创建监听
3. 给监听注册回调(多种处理方式)
	* `name:{ handle(){} }` 传入为对象去handler字段
	* `name(){}` 传入为函数直接监听回调
	* `name: 'getName'` 传入为字符串就去实例上获取回调
4. 调用`vm.$watch`
	* 判断是否立即执行回调
	* 每个`watch`配发`watcher(监听的key，callback，options)`
5. 监听的数据变化时，通知`watch-watcher`更新，然后使用`updata()`更新数据