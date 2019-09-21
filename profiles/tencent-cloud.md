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
* 实例启动
* web端登录实例
* SSH 秘钥创建

## 服务器环境设置
### 安装node
* 根据云主机系统选择node版本
* 登录云主机 并 下载node安装包(记住安装路径，免得下载完找不到安装包)
	* 方式一：直接下载

	```
	wget http://nodejs.org/dist/v8.9.4/node-v8.9.4-linux-x64.tar.gz
	```
	* 方式二：本地拷贝 拷贝到云主机地址下的文件夹

	```
	scp /local/file/path  root@111.230.105.177:/tmp
	```
* 解压安装包
	* 由于安装包是.xz格式的，我们首先需要先解压

	```
	tar -zxvf node-v8.9.4-linux-x64.tar.gz
	```	
* 解压成功会在当前文件夹生成 node 包，包名过长过复杂可重命名

	```
	mv node-v8.9.4-linux-x64 node
	```	
* 全局设置 node 环境变量 找到.bash_profile文件并修改
	```
	vim ~/.bash_profile
	```
	PATH=$PATH:$HOME/bin:/usr/local/src/node/bin
	```
	source ~/.bash_profile
	```
