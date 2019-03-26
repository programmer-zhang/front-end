### grunt angular项目sass报错
* sass模块报错
	* 重装sass
		* gem install sass
		* sass -v查看是否安装成功
		* 权限问题：sudo gem install sass
	* 重装ruby
		* 使用homebrew获得ruby：brew install ruby


### &lt;input type="number"&gt;maxlength失效
* 移动端页面为了方便用户交互通常将手机号输入框和数字输入框设置type属性，在用户触发fcous（）时手机键盘自动弹出数字键盘。
* 解决方案：修改为&lt;input
type="tel"&gt;可以起到同样的效果

### npm包缺失报错
* 删除项目中的node_modules 并尝试重新 npm install,如重新安装之后还是报相同错误，请尝试下面的做法
* 删除本地项目node_modules包，复制相同项目相同操作系统的依赖包
* 指定依赖和版本安装
	* 如：缺少webpack-merge包,首先查看是否安装了webpack-merge模块`npm ls --depth=0` 

	* 如果没有重新安装指定依赖和版本
`npm install webpack-merge --save-dev`

### swiper滑动失效
* 使用swiper会碰到模块滑动失败的问题，这种问题在vue、angular这种框架下发生几率高，原因在于模块数据未加载完成就开始初始化swiper导致
* 解决方案：
	* 一：使用settimeout()进行延时，不易过长，300毫秒足够，但是要注意的是js的作用域问题
	* 二：使用swiper自带的属性进行自动初始化
	
```
swiper1 = new Swiper('.swiper-container', {  
    initialSlide :0,  
    observer:true,//修改swiper自己或子元素时，自动初始化swiper  
    observeParents:true//修改swiper的父元素时，自动初始化swiper  
});  
```
* 个人建议使用第一种，两种结合来用也可以，第一种方案可以完美解决失效问题。

### git 初次提交远程仓库出现refusing to merge unrelated histories报错
* 先pull，因为两个仓库不同，报错 ` refusing to merge unrelated histories`，无法pull

* 因为他们是两个不同的项目，要把两个不同的项目合并，git需要添加一句代码，在git pull，这句代码是在git 2.9.2版本发生的，最新的版本需要添加--allow-unrelated-histories

* 假如我们的源是origin，分支是master，那么我们需要这样写`git pull origin master --allow-unrelated-histories`需要知道，我们的源可以是本地的路径

### 解决每次push都要输入password和username的方法
* push 到 Github 每次都要输入密码，原因是使用了 Https 的方式添加远程仓库
* 解决方案：
	* 创建SSH Key，把id_rsa.pub中的内容添加到 Github 的SSH keys。
	* 或者使用本机已存在的SSH key
	* git remote rm origin
	* git remote add origin git@github.com:XXX.git

### 微信小程序开发数据传输限制
* 报错内容 'invokeWebviewMethod 数据传输长度为 xxxxx 已经超过最大长度 1048576'
* 原因：wx:for 渲染的数据长度过长,每次setData的时候传入的更新数据太多
* 解决方法：
	* 后台的数据接口传值简化
	* 前端优化数据，只保留需要的数据

### VueSSR造成内存溢出
* https://gitbook.cn/books/591170568b2c1f0f85f3b8fb/index.html
* https://blog.csdn.net/qq_34309704/article/details/80852449
* http://www.cnblogs.com/chuaWeb/p/5196330.html
* https://www.cnblogs.com/alongWind/p/7683624.html
* https://yq.aliyun.com/articles/682846