## 阅读本文您将收获
* `Vue computed` 原理

### Vue computed 原理
* 设置 `computed` 的 `getter` ，若执行了 `computed` 的函数，会去读取 `data` 值，就会触发 `data` 的 `getter` ，从而建立`data`的依赖关系
* 首次`mounted`的值，会执行`vm.computed`对应的`getter`，没有`getter`的是赋值函数
* 若`computed`的属性值依赖其他属性值，会将`target`暂存在栈中，先进行其他的依赖收集