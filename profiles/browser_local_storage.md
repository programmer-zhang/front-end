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

## localStorage
* **生命周期:**永久，除非用户清除浏览器中的 `localStorage` 信息，否则永远存在
* **存储容量:**存放数据大小一般为5M(针对一个域名)
* **使用注意:**仅在浏览器中保存，不参与与服务器的通信
* **使用方式:**

```
localStorage.setItem("key","value"); // 以“key”为名称存储一个值“value”
localStorage.getItem("key"); // 获取名称为“key”的值
localStorage.removeItem("key"); // 删除名称为“key”的信息
localStorage.clear();​ // 清空localStorage中所有信息
```

## sessionStorage
* **生命周期:** 仅在当前会话下有效，关闭页面或浏览器后被清楚
* **存储容量:**存放数据大小一般为5M(针对一个域名)
* **使用注意:** 仅在浏览器中保存，不参与与服务器的通信
* **使用方式:**

```
sessionStorage.setItem("key","value"); // 以“key”为名称存储一个值“value”
sessionStorage.getItem("key"); // 获取名称为“key”的值
sessionStorage.removeItem("key"); // 删除名称为“key”的信息
sessionStorage.clear();​ // 清空sessionStorage中所有信息
```