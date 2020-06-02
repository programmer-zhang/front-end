> 对于前端开发来说，跨域是个挥之不去的知识点，也是面试中经常被问到的点

> 平时工作中需要用到时 webpack-dev-server 和负责 服务器 配置的大佬会帮我们搞定，但是作为有担当、负责任的青年我们不能就这么混过去了

> 接下来就开始展现真正的技术了---跨域问题的产生原因及部分常用的跨域请求解决方式详解

## 阅读本文您将收获到：
* 同源策略的概念
* 跨域的几种常用解决方式
* 合理地封装函数

## 需要跨域的根本原因--同源策略
### 同源策略的 MDN 简述
* 同源策略限制从一个源加载的文档或脚本如何与来自另一个源的资源进行交互。这是一个用于隔离潜在恶意文件的关键的安全机制
* 详情请点击[官方文档](https://developer.mozilla.org/zh-CN/docs/Web/Security/Same-origin_policy)查看，这里不再赘述
* 同源策略仅存在于浏览器端，服务器端不存在同源策略

### 同源策略简述
* 限制了从同一个源加载的文档或脚本如何与来自另一个源的资源进行交互
* 是基于安全的角度进行的限制

### 同源策略造成的结果
* js脚本只能访问或者请求相同协议，相同domain(网址/ip)，相同端口的页面

### 不受同源策略影响的部分
* `<script>` 标签 、css样式表、图片资源文件
 
## 一、jsonp实现跨域请求
### 原理
* 利用 `<script>` 标签可以链接到不同源的 `js` 脚本，从而达到跨域目

### 实现方式
* 利用 `<script>` 标签的特性，将数据使用 `json` 格式用一个函数包裹起来
* 在进行访问的页面中定义一个相同函数名的函数，因为 `<script>` 标签src引用的js脚本到达浏览器时会执行，而我们有定义了一个同名的函数，所以json格式的数据，就做为参数传递给了我们定义的同名函数了

### 优缺点
* `<script>、<link>、<img />` 标签引入外部资源，都是get请求，那么就决定了jsonp跨域传输一定是get请求

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
      // 由于后端把返回的数据放在方法的参数里，所以这里能拿到res
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
  url: 'http://www.xxx.com/api',
  data: {
    // 传参
    msg: 'xxxxxx'
  }
}).then(res => {
  console.log(res)
})
```

## 二、使用 iframe + form 进行跨域请求
### 实现方式

```
const requestPost = ({url, data}) => {
  // 首先创建一个用来发送数据的iframe.
  const iframe = document.createElement('iframe')
  iframe.name = 'postAjax'
  iframe.style.display = 'none'
  document.body.appendChild(iframe)
  const form = document.createElement('form')
  const node = document.createElement('input')
  // 注册iframe的load事件处理程序,如果你需要在响应返回时执行一些操作的话.
  iframe.addEventListener('load', function () {
    console.log('post success')
  })

  form.action = url
  // 在指定的iframe中执行form
  form.target = iframe.name
  form.method = 'post'
  for (let name in data) {
    node.name = name
    node.value = data[name].toString()
    form.appendChild(node.cloneNode())
  }
  // 表单元素需要添加到主文档中.
  form.style.display = 'none'
  document.body.appendChild(form)
  form.submit()

  // 表单提交后,就可以删除这个表单,不影响下次的数据发送.
  document.body.removeChild(form)
}
// 使用方式
requestPost({
  url: 'http://www.xxx.com/api',
  data: {
    msg: 'xxxx'
  }
})

```
## 三、CORS(跨域资源共享 Cross-origin resource sharing)进行跨域请求
### 分类
* 简单请求
	* 请求方法是以下几种: GET、POST、HEAD
	* HTTP头信息是以下几种: Accept、Accept-Language、Content-Language、Last-Event-ID、Content-Type ( 只限于三个值application/x-www-form-urlencoded、multipart/form-data、text/plain )
* 非简单请求
	* 除去简单请求之外的请求

### 实现方式
* `CORS` 需要浏览器和后端同时支持
* 需要服务端设置 `Access-Control-Allow-Origin` 就可以开启 `CORS`

### CORS 简单请求
* 服务端配置好的前提下，简单请求直接请求就可以

### CORS 非简单请求
* 非简单请求在请求时会发送两次请求
	* 第一次是预检测请求，返回的状态码为204
	* 第二次请求为预检测请求通过后才会发送真实请求

## 四、利用服务器中转
### 原理
* 因为同源策略是针对客户端的，在服务器端没有什么同源策略，是可以随便访问的

### 实现方式
* 客户端先访问 同源的服务端代码，该同源的服务端代码，使用httpclient等方法，再去访问不同源的 服务端代码，然后将结果返回给客户端

## 五、window.postMessage
### 原理
* 从广义上讲，一个窗口可以获得对另一个窗口的引用 `(比如 targetWindow = window.opener)`，然后在窗口上调用 `targetWindow.postMessage()` 方法分发一个  `MessageEvent` 消息。接收消息的窗口可以根据需要自由处理此事件。传递给 `window.postMessage()` 的参数（比如 message ）将通过消息事件对象暴露给接收消息的窗口

### 语法
* `otherWindow.postMessage(message, targetOrigin, [transfer]);`
	* otherWindow: 其他窗口的一个引用，比如iframe的contentWindow属性、执行window.open返回的窗口对象、或者是命名过或数值索引的window.frames
	* message: 将要发送到其他 window的数据
	* targetOrigin: 通过窗口的origin属性来指定哪些窗口能接收到消息事件，其值可以是字符串"*"（表示无限制）或者一个URI
	* transfer(可选): 是一串和message 同时传递的 Transferable 对象. 这些对象的所有权将被转移给消息的接收方，而发送一方将不再保有所有权