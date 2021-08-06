> 浏览器的本地存储死我们开发过程中经常会用到的知识点，也是页面间数据通信的一种方式，本篇文章为大家讲解浏览器的几种存储方式及详细使用方法。

## 阅读本文您将收获
* 浏览器本地存储分为哪几种
* 浏览器本地存储几种方式的区别

## 浏览器本地存储有哪些？
* 浏览器本地存储主要是以下几种方式，其中前三种为实践中常用的三种存储方式
	* cookie
	* localStorage
	* sessionStorage
	* indexDB

## cookie
> cookie最初被创建出来并不是为了本地存储使用的，是解决HTTP的状态管理的产物。

> 因为HTTP协议是一个无状态协议，客户端和服务端的通信为了解决相互识别的问题，出现了cookie

* **生命周期:** 在 `cookie` 设置的过期时间之前一直有效，即使窗口或者浏览器关闭
* **存储容量:** 存放数据大小为4K, 有存储个数限制（各浏览器不同），一般不超过20个
* **使用注意:** 与服务器端通信，每次都会携带在HTTP头中，cookie存储数据过多会带来性能问题。同时cookie以纯文本的形式在客户端和服务端中传递，存在被截获后篡改的风险。另外在 `HttpOnly: false` 的情况下，cookie信息能直接通过JS脚本来读取
* **使用方式:**

```
document.cookie="username=John Doe; expires=Thu, 18 Dec 2043 12:00:00 GMT; path=/"; // 创建一个cookie，或修改一个cookie
document.cookie = "username=; expires=Thu, 01 Jan 1970 00:00:00 GMT"; // 删除cookie只需要将过期时间提前即可
```

> Cookie 的更多处理方式请看这篇文章， [JS针对 Cookie 的操作](../profiles/js针对cookie的操作.md)

## localStorage
* **生命周期:** 永久，除非用户清除浏览器中的 `localStorage` 信息，否则永远存在
* **存储容量:** 存放数据大小一般为5M(针对一个域名)
* **使用注意:** 仅在浏览器中保存，不参与与服务器的通信
* **使用方式:**

```
localStorage.setItem("key","value"); // 以“key”为名称存储一个值“value”
localStorage.getItem("key"); // 获取名称为“key”的值
localStorage.removeItem("key"); // 删除名称为“key”的信息
localStorage.clear();​ // 清空localStorage中所有信息
```

## sessionStorage
* **生命周期:** 仅在当前会话下有效，关闭页面或浏览器后被清楚
* **存储容量:** 存放数据大小一般为5M(针对一个域名)
* **使用注意:** 仅在浏览器中保存，不参与与服务器的通信
* **使用方式:**

```
sessionStorage.setItem("key","value"); // 以“key”为名称存储一个值“value”
sessionStorage.getItem("key"); // 获取名称为“key”的值
sessionStorage.removeItem("key"); // 删除名称为“key”的信息
sessionStorage.clear();​ // 清空sessionStorage中所有信息
```

## indexDB
> IndexDB是运行在浏览器中的非关系型数据库, 本质上是数据库。

> 为了解决上述几种本地存储方案不支持海量数据存储，不支持查找，不支持索引的缺点而产生。

* **存储容量:** 理论上这个容量是没有上限的
* **使用特点:**
	* 支持键值对存储: 所有数据类型都可以直接存入，包括对象，数据都将以'键值对'的形式保存，每个数据记录都有对应的独一无二的主键，不可重复。
	* 异步设计: 对其进行操作时不会锁死浏览器，防止大量数据读写时拖慢网页。
	* 支持事务: 在一系列操作中只要有一步失败整个事务就取消，数据库会自动回滚到之前的状态。
	* 同源限制: 每个indexDB数据库对应其自身域名
	* 支持二进制存储: 不仅可以存储字符串，还可以存储二进制数据
* **使用方式:**
	* 阮一峰老师的文档写的很清楚了，这里不再赘述，[链接请戳这里](https://www.ruanyifeng.com/blog/2018/07/indexeddb.html)