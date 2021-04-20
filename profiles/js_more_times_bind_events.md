## 阅读本文您将收获
* 引发JS 多次绑定事件的原因
* JS 多次绑定事件的解决方案

## 我明明只绑定了一次事件，却执行了多次
* 原因：JS 中的绑定事件会累加
* 例如我们在手写分页组件拿取数据时，如果使用传统的 `addEventListener ` 或 `bindEvent` 或 JQuery中 `on` 绑定点击事件时，每次获取到新的数据时，就会为该class绑定一次事件，即造成了事件的累加。

## 解决方案
### 一: 及时清除或更新已绑定事件的节点
* 点击事件执行时，将已绑定事件的DOM节点清除，再重新动态创建一个新的DOM节点，再绑定事件
* 缺点: 不够灵活，会造成多余的性能浪费

### 二: 使用JQuery的one()方法
* 为元素绑定一个一次性的事件处理函数，这个事件处理函数只会被执行一次。

```
$('#clickDom').one('click', function() {
	// do something
})
```

### 三: 每次绑定时间之前先解绑事件
* 在绑定事件之前，现将该事件在节点上解绑，然后重新绑定，相当于将点击事件更新

```
$('#clickDom').unbind('click').bind('click', function() {
	// do something
})
$('#clickDom').off('click').on('click', function() {
	// do something
})
```