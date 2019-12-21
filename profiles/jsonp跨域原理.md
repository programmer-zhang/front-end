### js中的同源策略
* js脚本只能访问或者请求相同协议，相同domain(网址/ip)，相同端口的页面。

## jsonp跨域原理
* 前端页面中有三种资源是可以与当前页面本身不同源：js脚本、css样式文件、图片资源文件
* 例：css文件会对页面进行repaint，img会将图片渲染出来，`<script>`标签引入的脚本会被执行。
* jsonp就是利用`<script>`标签可以链接到不同源的js脚本，从而达到跨域目
* 利用script标签的特性，将数据使用json格式用一个函数包裹起来
* 然后在进行访问的页面中定义一个相同函数名的函数，因为 script 标签src引用的js脚本到达浏览器时会执行，而我们有定义了一个同名的函数，所以json格式的数据，就做为参数传递给了我们定义的同名函数了
* jsonp的含义是：json with padding 
* script、link、img标签引入外部资源，都是get请求，那么就决定了jsonp跨域传输一定是get请求

### 其他跨域方法
* 因为同源策略是针对客户端的，在服务器端没有什么同源策略，是可以随便访问的，所以我们可以通过下面的方法绕过客户端的同源策略的限制：客户端先访问 同源的服务端代码，该同源的服务端代码，使用httpclient等方法，再去访问不同源的 服务端代码，然后将结果返回给客户端，这样就间接实现了跨域
* 在服务端开启cors也可以支持浏览器的跨域访问。cors即：Cross-Origin Resource Sharing 跨域资源共享

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
    <script src='http://localhost:9871/api/jsonp?msg=helloJsonp&cb=jsonpCb' type='text/javascript'></script>
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