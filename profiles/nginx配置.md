上一篇写了点[nginx 的架构](./nginx基础.md)，nginx开发过程中可能架构相关的知识需要的不多，但是了解些nginx架构方便的知识对于我们更深层次地了解nginx配置有很大的帮助
## nginx安装
* MAC上装有homebrew，使用其下载nginx `brewinstall nginx`
* nginx下载成功后，有三个目录比较重要
	* `/usr/local/etc/nginx`nginx的默认安装目录
	![nginx安装目录](../images/nginx安装目录.jpg)
	* ` /usr/local/etc/nginx`
	* `/usr/local/var/www` nginx的服务器文件存放位置
## nginx相关操作
* 进入`/usr/local/etc/nginx`
* 执行`sudo nginx -c nginx.conf`，nginx启用默认配置启动
* `sudo nginx -s reload` 优雅重启Nginx
* `sudo nginx -s quit` 退出nginx
* 