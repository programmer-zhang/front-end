## 阅读本文您将收获
* JS 事件为什么会发生一次绑定多次执行
* 如何解决原生 JS DOM 事件多次绑定执行的问题

## 一次绑定多次执行到底为啥
* 当我们在开发的过程中，针对DOM节点进行绑定事件，明明我们只使用JQ的on()方法或者addEventListener()绑定了一次事件，但在代码执行过程中，可能会遇到事件被多次执行情况，其实这是方法的特性引起的问题。

```
<input id="bindDomBtn" type="button" value="绑定事件">
<input id="showBtn" type="button" value="展示按钮">
```