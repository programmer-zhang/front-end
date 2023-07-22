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
	
	cp /usr/local/nginx/sbin/nginx /usr/local/nginx/sbin/nginx.bak #备份原有nginx配置文件
	
	cp /root/nginx/objs/nginx /usr/local/nginx/sbin #替换原有nginx配置文件
	```
	
* 确认 Nginx SSL 模块安装成功
	
	```
	./nginx -V
	``` 
	
	* 注意：这里使用大写的`V`，`v` 只会显示版本号，`V` 显示更多配置信息。
	

### 配置SSL证书
* 解压缩下载好的证书，包括pem和key文件
* 将证书上传到服务器，不要忘记上传地址。

### nginx.conf 配置
* 打开 nginx 配置文件
	* `vi /usr/local/nginx/conf/nginx.conf`
* 修改 443 端口权限
	* 要使用https服务需要监听443端口，nginx.conf 已经预留出server，只需要修改权限即可。
	
	```
	# 监听443端口
	server {
        	listen 443;
        	server_name example.com;
        	ssl on;
        	ssl_certificate /usr/local/nginx/key/server.pem;
        	ssl_certificate_key /usr/local/nginx/key/server.key;
        	ssl_session_timeout  5m; #session超时时间(可删除)
        	ssl_protocols TLSv1 TLSv1.1 TLSv1.2;     #指定SSL服务器端支持的协议版本(可删除)
        	ssl_ciphers  HIGH:!aNULL:!MD5; # (可删除)
        	#ssl_ciphers  ALL：!ADH：!EXPORT56：RC4+RSA：+HIGH：+MEDIUM：+LOW：+SSLv2：+EXP;    #指定加密算法(可删除)
       	 ssl_prefer_server_ciphers   on;    #在使用SSLv3和TLS协议时指定服务器的加密算法要优先于客户端的加密算法(可删除)
	}
	```

* 监听80端口
	* 80端口是默认端口，监听80端口重定向到 https 端口上

	```
	server {
        	listen 80;
        	server_name example.com;
        	rewrite ^(.*) https://$server_name$1 permanent;
	}
	```

### 重启Nginx
* 重启成功就大功告成了

```
./nginx -s reload
./nginx -s stop
./nginx 
```