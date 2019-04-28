> 此文件主要留存日常代码书写过程中的 微信相关开发 相关的报错与解决方案，部分方案参考自网络，如有侵权，请联系chinajnzhang@hotmail.com删除

### 微信小程序开发数据传输限制
* 报错内容 'invokeWebviewMethod 数据传输长度为 xxxxx 已经超过最大长度 1048576'
* 原因：wx:for 渲染的数据长度过长,每次setData的时候传入的更新数据太多
* 解决方法：
	* 后台的数据接口传值简化
	* 前端优化数据，只保留需要的数据

### 微信安全域名的坑
* [微信网页授权官方文档](https://mp.weixin.qq.com/wiki?t=resource/res_main&id=mp1421140842)
* 具体的微信安全域名配置就不说了，大家都知道的，也可自行百度
* 略微提几点
	* 微信安全域名配置前需要在该域名根目录下放入微信官方的配置文件，并且可以访问到。
	* 域名配置仅配置域名就可以，不需要加上传输协议名称。
	* 大家可根据需要直接配置一级或者二级域名，避免以后重复添加和修改，微信安全域名同一个微信公众号仅可配置三个微信安全域名，每月仅支持修改三次。

### 微信开发踩过的那些坑
* invalid URL domain（域名绑定失败或者域名不存在）
	* 检查后台是否设置：右上角公众号名称--功能设置--JS接口安全域名
	* 检查代码里的appid和公众号后台的id是否一致
	* 一级域名，非80端口需要带端口号
* permission denied（接口权限问题）
	* 有的接口需要认证之后才可以使用
* invalid signature（大部分微信分享失效是因为这个报错）
	* 微信js文件未引入或者引入错误
		* 看传输协议，HTTPS引入外部HTTP文件有问题，将微信的JS文件传输协议改成HTTPS，该文件支持HTTPS传输
	* 生成签名的url(使用jssdk的页面地址，这个页面地址可以在浏览器访问)，包含“？”号后面的所有参数，不包含“#”号后面的值
		* 官方提供的解决方案 使用`encodeURIComponent(location.href.split('#')[0])即可`
	   * 未知原因会造成页面请求的url被encode，需要多加一层encode
* debug模式下config:OK但是分享不成功的奇葩原因
	* 客户端6.7.2及JSSDK 1.4.0以上版本将开始更新新的微信分享接口
	* [微信分享接口API更新](https://mp.weixin.qq.com/wiki?t=resource/res_main&id=mp1421141115)
	* 在按照微信指引使用JSSDK 1.4.0之后，将微信分享接口调整为`wx.updateAppMessageShareData、updateTimelineShareData`失效
	* 不知道是微信的原因还是什么原因，使用旧版的分享接口后完全没问题

### 浏览器端模拟微信打开

* UA-Android
Mozilla/5.0 (Linux; U; Android 4.1.2; zh-cn; Chitanda/Akari) AppleWebKit/534.30 (KHTML, like Gecko) Version/4.0 Mobile Safari/534.30 MicroMessenger/6.0.0.58_r884092.501 NetType/WIFI

* UA-ios
Mozilla/5.0 (iPhone; CPU iPhone OS 8_0 like Mac OS X) AppleWebKit/600.1.4 (KHTML, like Gecko) Mobile/12A365 MicroMessenger/5.4.1 NetType/WIFI