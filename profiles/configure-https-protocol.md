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
* 检查Nginx是否安装SSL模块
	* 在Nginx的安装目录下查看版本号，默认安装路径为 `/usr/local/nginx`，Nginx可执行文件在`/usr/local/nginx/sbin` 下，使用指令检查SSL模块是否存在：

	```
	nginx -v
	```

	* 如果输出的Nginx配置种出现 `configure arguments: --with-http_ssl_module`， 则表示机器上的Nginx已完装SSL模块

* 安装Nginx SSL模块

```
./configure --prefix=/usr/local/nginx --with-http_stub_status_module --with-http_ssl_module
```

* 编译
	

	```
	make
	```
	
	* 编译成功后，目录下会多出一个 objs 文件夹，文件夹内会有以 'nginx' 命名的文件。
	* 使用新出现的 nginx 替换之前 `sbin` 目录下的 nginx，同时停掉 nginx 服务。
	* 注意：这里需要备份下之前的文件

	```
	./nginx -s stop #停止nginx服务
	
	cp /root/nginx/onjs/nginx /usr/local/nginx/sbin
	```
	
* 确认 Nginx SSL 模块安装成功
	
	```
	./nginx -V
	``` 
	

### 配置SSL证书

### nginx.conf 配置

### 重启Nginx