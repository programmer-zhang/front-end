## 阅读本文您将收获
* `JavaScript` 中的 `Proxy` 是什么？能干什么？
* `Vue3.0` 开始为什么 `Proxy` 代替 `Object.defineProperty`

## `Proxy` 是什么
> 解释参考MDN，[链接直达](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Proxy)。

### 名词解释
* Proxy 对象用于定义基本操作的自定义行为（如属性查找、赋值、枚举、函数调用等）。

### 语法
* `const p = new Proxy(target, handler)`
	* `target`: 要使用 Proxy 包装的目标对象（可以是任何类型的对象，包括原生数组，函数，甚至另一个代理）
	* `handler`: 一个通常以函数作为属性的对象，各属性中的函数分别定义了在执行各种操作时代理 p 的行为
* 简而言之：`target` 就是你想要代理的对象；而 `handler` 是一个函数对象，其中定义了所有你想替 `target` 代为管理的操作对象，包含了：
	* `handler.getPrototypeOf()`: `Object.getPrototypeOf` 方法的捕捉器
	* `handler.setPrototypeOf()`: `Object.setPrototypeOf` 方法的捕捉器
	* `handler.isExtensible()`: `Object.isExtensible` 方法的捕捉器
	* `handler.preventExtensions()`: `Object.preventExtensions` 方法的捕捉器
	* `handler.getOwnPropertyDescriptor()`: `Object.getOwnPropertyDescriptor` 方法的捕捉器
	* `handler.defineProperty()`: `Object.defineProperty` 方法的捕捉器
	* `handler.has()`: `in` 操作符的捕捉器
	* `handler.get()`: 属性读取操作的捕捉器
	* `handler.set()`: 属性设置操作的捕捉器
	* `handler.deleteProperty()`: `delete` 操作符的捕捉器
	* `handler.ownKeys()`: `Object.getOwnPropertyNames` 方法和 `Object.getOwnPropertySymbols` 方法的捕捉器
	* `handler.apply()`: 函数调用操作的捕捉器
	* `handler.construct()`: new 操作符的捕捉器

## `Proxy` 能干什么
### 直观的私有变量/拦截has...in...操作

### 验证属性

## Decorator
* ES7 中实现的 Decorator，相当于设计模式中的装饰器模式。如果简单地区分 Proxy 和 Decorator 的使用场景，可以概括为：Proxy 的核心作用是控制外界对被代理者内部的访问，Decorator 的核心作用是增强被装饰者的功能。只要在它们核心的使用场景上做好区别，那么像是访问日志这样的功能，虽然本文使用了 Proxy 实现，但也可以使用 Decorator 实现，开发者可以根据项目的需求、团队的规范、自己的偏好自由选择。