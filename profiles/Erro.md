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

### Vue Router 二级路径在Nginx转发后访问不通
* Vue cli config配置中的build    assetsPublicPath: '/',


### swiper在Vue项目中引入失败
* swiper若是本地js引入，需要放在`Vue  static`文件夹中，否则会被Vue打包后识别不到。

### 解决图形验证码接口返回文件流图片
* 将图片按照相关格式转码即可
* `<img class="image-code" :src="'data:image/png;base64,'+imgCodeUrl"/>`

### Git报错
* 报错内容: `There is no tracking information for the current branch. Please specify which branch you want to merge with.`
* 原因: 本地仓库未与远程仓库相关联,使用`git branch -vv`指令可以查看仓库的相互关联
* 解决方案: `git branch --set-upstream-to=origin/远程分支的名字  本地分支的名字`

### Nginx报错
* 报错内容：`unknown directive "xxx" in /usr/local/etc/nginx/servers/hmall-shop.conf:32`
* 原因：nginx配置书写错误
* 解决方案：根据nginx报错内容，合理修改不合语法的配置，有时一个末尾的空格都会引起错误