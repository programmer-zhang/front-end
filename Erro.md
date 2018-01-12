###grunt angular项目sass报错
* sass模块报错
	* 重装sass
		* gem install sass
		* sass -v查看是否安装成功
		* 权限问题：sudo gem install sass
	* 重装ruby
		* 使用homebrew获得ruby：brew install ruby


###&lt;input type="number"&gt;maxlength失效
* 移动端页面为了方便用户交互通常将手机号输入框和数字输入框设置type属性，在用户触发fcous（）时手机键盘自动弹出数字键盘。
* 解决方案：修改为&lt;input
type="tel"&gt;可以起到同样的效果

###npm包缺失报错
* 