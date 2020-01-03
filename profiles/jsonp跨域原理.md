> 对于前端开发来说，跨域是个挥之不去的知识点，也是面试中经常被问到的点

> 平时工作中 webpack-dev-server 和负责 服务器 配置的大佬会帮我们搞定，但是作为有担当、负责任的青年我们不能就这么混过去了

> 接下来就开始展现真正的技术了---跨域问题的产生原因及部分常用的解决方式详解

## 需要跨域的根本原因--同源策略
### 同源策略的 MDN 详细概念
* 详情请点击[官方文档](https://developer.mozilla.org/zh-CN/docs/Web/Security/Same-origin_policy)查看，这里不再赘述
* 浏览器不

### 同源策略简述
* 限制了从同一个源加载的文档或脚本如何与来自另一个源的资源进行交互
* 是基于安全的角度进行的限制

### 同源策略造成的结果
* js脚本只能访问或者请求相同协议，相同domain(网址/ip)，相同端口的页面

### 不受同源策略影响的部分
* `<script>` 标签 、css样式表、图片资源文件
 
## 一、jsonp实现跨域
### 原理
* 利用 `<script>` 标签可以链接到不同源的 `js` 脚本，从而达到跨域目

### 实现方式
* 利用 `<script>` 标签的特性，将数据使用 `json` 格式用一个函数包裹起来
* 在进行访问的页面中定义一个相同函数名的函数，因为 `<script>` 标签src引用的js脚本到达浏览器时会执行，而我们有定义了一个同名的函数，所以json格式的数据，就做为参数传递给了我们定义的同名函数了

### 优缺点
* `script、link、img` 标签引入外部资源，都是get请求，那么就决定了jsonp跨域传输一定是get请求

### 简单版的JSONP跨域请求

```
<!DOCTYPE html>
<html>
  <head>
    <meta charset="utf-8">
  </head>
  <body>
    <script type='text/javascript'>
      // 后端返回直接执行的方法，相当于执行这个方法，
      // 由于后端把返回的数据放在方法的参数里，所以这里能拿到res。
      window.jsonpCb = function (res) {
        console.log(res)
      }
    </script>
    <script src='http://www.xxx.com/api/jsonp?msg=helloJsonp&cb=jsonpCb' type='text/javascript'></script>
  </body>
</html>
```

### 封装一套JSONP请求工具

```
/**
 * JSONP请求工具
 * @param url 请求的地址
 * @param data 请求的参数
 * @returns {Promise<any>}
 */
const request = ({url, data}) => {
  return new Promise((resolve, reject) => {
    // 处理传参成xx=yy&aa=bb的形式
    const handleData = (data) => {
      const keys = Object.keys(data)
      const keysLen = keys.length
      return keys.reduce((pre, cur, index) => {
        const value = data[cur]
        const flag = index !== keysLen - 1 ? '&' : ''
        return `${pre}${cur}=${value}${flag}`
      }, '')
    }
    // 动态创建script标签
    const script = document.createElement('script')
    // 接口返回的数据获取
    window.jsonpCb = (res) => {
      document.body.removeChild(script)
      delete window.jsonpCb
      resolve(res)
    }
    script.src = `${url}?${handleData(data)}&cb=jsonpCb`
    document.body.appendChild(script)
  })
}
// 使用方式
request({
  url: 'http://www.xxx.com/api/jsonp',
  data: {
    // 传参
    msg: 'helloJsonp'
  }
}).then(res => {
  console.log(res)
})
```

## 二、使用 iframe + form 进行跨域
### 原理
* 使用

## 三、CORS
### 实现方式

### 其他跨域方法
* 因为同源策略是针对客户端的，在服务器端没有什么同源策略，是可以随便访问的，所以我们可以通过下面的方法绕过客户端的同源策略的限制：客户端先访问 同源的服务端代码，该同源的服务端代码，使用httpclient等方法，再去访问不同源的 服务端代码，然后将结果返回给客户端，这样就间接实现了跨域
* 在服务端开启cors也可以支持浏览器的跨域访问。cors即：Cross-Origin Resource Sharing 跨域资源共享