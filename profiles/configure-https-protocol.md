# 为你的网站配置 HTTPS

## 阅读本文您将收获
* HTTPS 协议是什么
* 如何免费配置HTTPS

## HTTPS 协议是什么
* 应用层协议
* 用于客户端与服务端通信，由客户端——通常是个浏览器——发出的消息被称作请求（request），由服务端发出的应答消息被称作响应（response）
* 无状态的
* 四次握手
* 更多关于HTTPS的知识请查看往期文章，请[点击这里查看【前端性能优化-网络传输层优化】](https://github.com/programmer-zhang/front-end/blob/master/profiles/%5B%E5%89%8D%E7%AB%AF%E6%80%A7%E8%83%BD%E4%BC%98%E5%8C%96%5D%E7%BD%91%E7%BB%9C%E4%BC%A0%E8%BE%93%E5%B1%82%E4%BC%98%E5%8C%96.md)

## 配置 HTTPS
### Nginx SSL模块安装
* 在配置SSL证书前，要确保机器上的Nginx已经安装了SSL模块，一般情况下自己安装的Nginx都是不存在SSL模块的。
* 检查Nginx是否安装

```
nginx -v
```

### 配置SSL证书

### nginx.conf 配置

### 重启Nginx