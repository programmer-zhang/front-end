# MacOS环境 本地安装 WordPress
* 安装homebrew,
	* 验证方式 `brew --version`
* MacOS 集成了PHP环境，所以不需要安装
	* 验证方式 `php =m|less`
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


---另写一篇---

* 本地 [手动下载wordpress](https://cn.wordpress.org/download/#download-install)
* 检查所需环境是否满足需要，MacOS下PHP等环境是系统集成其中的，不需要单独下载。
