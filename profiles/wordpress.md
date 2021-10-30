# MacOS环境 本地安装 WordPress
* 安装homebrew,
	* 验证方式 `brew --version`
* MacOS 集成了PHP环境，所以不需要安装
	* 验证方式 `php -m|less`
* 安装 Composer
	* 指令:(四条指令，分开执行) 
	
	```
	php -r "copy('https://install.phpcomposer.com/installer', 'composer-setup.php');"
	php composer-setup.php
	php -r "unlink('composer-setup.php');"
	mv composer.phar /usr/local/bin/composer
	```
	* 验证方式 
	![](../images/wordPress/download-composer.png)
	* 安装完成后，我们要为 Composer 来配置国内镜像（受不可抗力因素影响，官方镜像下载速度缓慢）。
	* Composer 支持项目加速和全局加速，我们这里没有 Composer 项目，所以选择全局加速。在命令行中执行如下命令：

	```
	composer config -g repo.packagist composer https://packagist.phpcomposer.com
	```


---
## 另写一篇
* https://blog.csdn.net/u012910985/article/details/48131801
* https://www.jianshu.com/p/063ec385b823
* xampp搭建： https://www.jianshu.com/p/f4862dfbfbc4

---
### 我的错误
### Mac os下 Apache 正常启动 localhost 无法访问服务器
* 一个查看apachectl 访问localhost失败查看原因的指令 `sudo /usr/sbin/httpd -k start` 或者 `sudo apachectl -k restart`，看到原因之后去查原因
	* 报错信息： `No code signing authority for module at /usr/libexec/apache3/libphp7.so specified in LoadModule directive. Proceeding with loading process, but this will be an error condition in a future version of macOS.
httpd: Syntax error on line 187 of /private/etc/apache2/httpd.conf: Cannot load libexec/apache3/libphp7.so into server: dlopen(/usr/libexec/apache3/libphp7.so, 10): image not found`
	* 原因： 查了下应该是MAC自带的php版本与apache版本不符合，需要重新安装个php

### 一些操作
* /etc/apache2  目录下查看Apache版本 `httpd -version`
* 查找php*.so安装位置 `php -i` => 搜索 `extension_dir`
	* 我的是 `extension_dir => /usr/lib/php/extensions/no-debug-non-zts-20180731 => /usr/lib/php/extensions/no-debug-non-zts-20180731`
	* 然后去上边这个路径下找libphp7.so文件
* 查看本机Apache启动php情况 https://blog.csdn.net/wtdask/article/details/83510757

* [官方安装文档](https://cn.wordpress.org/support/category/installation/)

### 安装过程
* 本地 [手动下载wordpress](https://cn.wordpress.org/download/#download-install)
* 检查所需环境是否满足需要，MacOS下PHP等环境是系统集成其中的，不需要单独下载。
	* PHP 环境验证方式 `php -m|less`
	* MySQL 安装与校验
	* Apache 校验 `apachectl -v`
* 启动 Apache 服务 `sudo apachectl start`
	* 启动校验，浏览器网址: `http://localhost`, 显示 `it works` 即为启动成功
	* Apache webserver服务默认根目录 `Library/WebServer/Document`
* 启动 PHP 环境
	* 修改 Apache 配置文件 文件位置: `/etc/apache2/httpd.conf`
	* 查找 `LoadModule php5_module libexec/apache2/libphp5.so` (php版本不同文件名字不同)，将注释去掉。
	* 重启apachectl `sudo apachectl restart`
* 移动wordpress路径，即可打开wordpress配置页面

## MAMP 安装 wordPress 
### 涉及软件 & 扩展
* MAMP
* wordPress 代码包

### 1.MAMP 下载安装
* 下载地址: [https://www.mamp.info/en/downloads/](https://www.mamp.info/en/downloads/)
* 选择合适的版本进行下载，MAMP 区分MacOS(intel 芯片版本) 和 MacOS(M1芯片版本)
	* 查看 MacOS 版本: 电脑左上角 小苹果图标 => 关于本机 => 概览中处理器信息

> 注: MAMP 分为基础版本和PRO版本，基础版本够用了，不用花钱买PRO版本

* 下载完成后正常安装就好，傻瓜式安装，一直继续就好，安装成功后启动台的图标为一个灰色的大象

### 2.WordPress 下载解压
* 下载地址: [https://wordpress.org/](https://wordpress.org/)
* 顶部栏 `GetWordPress`点击 => 下滑有个 下载按钮，下载下来并解压，解压成功后应该是一个有很多 .php 文件的文件夹。

### 3.启动 MAMP 并配置 phpMyAdmin
* 选择 web server 为 `Apache`，并启动服务。
![](../images/wordpress/mamp-start.png)

* 查看配置并确定端口号
	* MAMP 工具栏选择设置 => 查看 Ports 选项确定各服务端口号，可以使用 `MAMP default` 的端口号，即如图所示:
	![](../images/wordpress/mamp-ports.png)
 
* 启动服务，使用 Apache 的端口号打开配置页面， `MAMP default` 的端口号下地址为: `http://localhost:8888/MAMP/` 

> 注: 正常启动成功后浏览器会自动打开配置页面

* 如果安装和启动正常，你将看到下面的页面
![](../images/wordpress/mamp-index.png)

* 选择页面下方的 `MySQL` ,正常情况下首行会显示需要下载 `phpMyAdmin` 扩展，点击  `phpMyAdmin`,会跳转到 数据库 管理。
![](../images/wordpress/mamp-mysql.png)

* 

* 将文件根目录更改为 wordpress 文件夹
* 打开MAMP主页添加php拓展
	* `localhost:8888/MAMP` => MySQL => `You can administer your MySQL databases with phpMyAdmin.`
* 创建自己的数据库并链接，完成 wordpress 配置

