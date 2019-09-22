# 腾讯云CentOS下搭建个人网站
## 运行环境
### 本地环境
* MacBook Pro
* macOS Mojave 10.14.6

### 腾讯云主机
* CentOS 7.6 64位
* 1 核 1 GB 1 Mbps
* 高性能云硬盘

## 准备工作
* 服务器实例启动
* web端登录实例
* SSH 秘钥创建

## 服务器环境设置
### 手动安装node
* 根据云主机系统选择node版本
	* [NodeJS官方下载地址](https://nodejs.org/en/download/)
* 登录云主机 并 下载node安装包(记住安装路径，免得下载完找不到安装包)
	* 方式一：直接下载

	```
	wget http://nodejs.org/dist/v8.9.4/node-v8.9.4-linux-x64.tar.gz
	```
	* 方式二：本地拷贝 拷贝到云主机地址下的文件夹

	```
	scp /local/file/path  root@111.230.105.xxx:/tmp
	```
* 解压安装包
	* 由于安装包是.gz格式的，我们首先需要先解压

	```
	tar -zxvf node-v8.9.4-linux-x64.tar.gz
	```	
* 解压成功会在当前文件夹生成 node 包，包名过长过复杂可重命名

	```
	mv node-v8.9.4-linux-x64 node
	```	
* 全局设置 node 环境变量 
	* 找到.bash_profile文件

	```
	vim ~/.bash_profile
	```
	* 修改文件

	```
	PATH=$PATH:$HOME/bin:/usr/local/src/node/bin
	```
	* 保存文件

	```
	source ~/.bash_profile
	```
* 检查 NodeJS 是否安装成功 `node -v`

	![](../images/node-success.png)

* 检查 云服务 是否正常
	* 在机器中创建server.js
	
	```
	// 创建HTTP服务
	var http = require('http');
	http.createServer(function (req, res) {
		res.writeHead(200, {'Content-Type': 'text/plain'});
		res.end('Welcome Node.js');
	}).listen(8080, '0.0.0.0'); //注意这里是监听 0.0.0.0 的端口
	console.log('Server running at http://0.0.0.0:8080/');
	```
	* 启动 server 服务 `node server.js`
* 访问机器外网IP，访问成功即为云服务器搭建成功

	![](../images/server-success.png)

* 访问机器外网IP，访问失败请按照腾讯云服务器自检文档进行自检
	* [自检地址](https://cloud.tencent.com/document/product/213/14633)

## 项目创建
### Vue项目本地创建
### 项目本地启动
##### 本地 node 启动访问 (开发、测试、生产环境启动)
##### 本地 nginx 访问静态包 (测试、生产环境)
### git 关联并上传
### 服务器添加项目
### 服务器启动项目
##### 使用 PM2 进行 node 启动
##### nginx 访问静态包
### 访问网站

	
