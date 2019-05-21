> 现如今围绕微信生态相关开发已经非常常见，本期带来如何通过 `qrcode.js` 实现微信内置浏览器动态生成二维码并能够长按识别

## 说几个知识点
* 微信长按弹出识别选项的原理
	* 微信客户端检测到用户长按`img`标签
	* 微信主动进行截屏并识别图片，二维码识别采用的是截屏而不是通过`img`标签
	* 微信识别成功后执行相关操作
* Base64
	* Base64是网络上最常见的用于传输8Bit字节码的编码方式之一，Base64就是一种基于64个可打印字符来表示二进制数据的方法
* Blob
	* HTML5中的Blob对象与MySQL中的BLOB对象有区别，HTML5中的Blob对象除了存放二进制数据外还可以设置这个数据的MINE类型，这相当于对文件的存储，其它很多二进制对象也是从这个对象继承的
* canvas.toDataURL([type, encoderOptions])
	* type: 指定图片类型，默认值`image/png`
	* encoderOptions: 为image/jpeg或image/webp类型的图片设置图片质量，取值0-1，超出则以默认值0.92替代
	* 作用: 通过canvas进行转化图片

## 准备工作
* 结合微信规范明确需求
	* 微信img标签通过src属性可实现长按弹出选项(保存至手机，图片为二维码的情况下会出现识别二维码)
	* 二维码图片若为本地图片或服务器图片(即不需要进行动态生成)只需要正常编写代码即可实现
	* 微信针对内置浏览器内的页面图片有着自己的一套适应逻辑与规范，canvas的图片和base64编码格式的图片在安卓与ios手机上会出现不同的问题
* 确定实现方案
	* 本例采用第三方js库实现生成二维码
	* 针对生成的base64编码的图片微信无法长按识别需要在前端进行格式和image对象重新转换
	* 生成的图片弹窗展示，避免出现其他元素影响微信识别率

## 技术实现
> 本例的技术实现方案均在Vue项目环境下实现的

### 引入第三方js库
* 提供两种引入方式，两种方式是不同的js库，方便大家选择和使用
	* 本地引入 `qrcode.js`
	
	```
	// qrcode.js官方GitHub文档: https://github.com/davidshimjs/qrcodejs
	<script src="static/js/qrcode.js"></script>
	```
	* npm 引入 `qrcodejs2`
	
	```
	npm install qrcodejs2
	import qrCode from 'qrcodejs2'
	```

### 组件中调用
* HTML

	```
	<div class="qrcode-panel" id="qrcode"></div>
	```
* JS
	* 简单调用
	
	```
	new QRCode(document.getElementById('qrcode'), 'your content');
	// new QRCode(element, option)
	// element 显示二维码的元素或该元素的 ID
	// option  参数配置
	```
	* 标准调用
	
	```
	var qrcode = new QRCode(document.getElementById("qrcode"), {
		text: "https://www.xxx.com?did=123456&id=123&userid=456",
		width: 160, 		//图像宽度
		height: 160,		// 图像高度
		render: 'canvas',		// 生成格式(table 和 canvas)
		colorDark : "#000000",		//前景色
		colorLight : "#ffffff",		//背景色
		correctLevel : QRCode.CorrectLevel.H    // 容错级别
	});
	// 容错级别，可设置为：
	QRCode.CorrectLevel.L（最大 7% 的错误能够被纠正）
	QRCode.CorrectLevel.M（最大 15% 的错误能够被纠正）
	QRCode.CorrectLevel.Q（最大 25% 的错误能够被纠正）
	QRCode.CorrectLevel.H（最大 30% 的错误能够被纠正）
	```
	* 其他公共方法
	
	```
	QRCode.makeCode(text) // 	设置二维码内容
	QRCode.clear()  // 	清除二维码
	```

### 重置 Image 对象
* 重置的原因是原JS生成的 image 和 canvas 对象无法在微信端长按识别

	```
	var canvas = document.getElementsByTagName('canvas')[0];
	var img = this.convertCanvasToImage(canvas);
	document.getElementById("qrcode").append(img);
	
	convertCanvasToImage(canvas) {
		//新建Image对象
		var image = new Image();
		// canvas.toDataURL 返回的是一串Base64编码的URL
		image.src = canvas.toDataURL("image/png");
		image.id = 'qrcodeImg';
		return image;
    }
	```

### 后续细节处理
* 至此，一个能够满足长按识别的动态二维码已经生成，不继续处理的话会有两张二维码，长按对比就能看出，qrcode.js生成的二维码长按无法识别，而经过重置之后的对象是可以实现此功能的。
* 我的处理方式是两个二维码都保留，将二维码图片进行重新定位，将重置的二维码图片置于不能识别二维码上层，不去频繁操作DOM节点的显示隐藏。

### 另：微信内置浏览器图片保存
* 上述教程可以实现动态生成二维码进行保存和长按识别，但是如果需要将HTML内容生成canvas保存就存在问题了。
* 针对保存需要注意的几个问题：
	* canvas禁止跨域
	* 安卓微信长按不能保存base64图片
	* 微信限制Blob类型图片的保存
	* 使用 `canvas.toDataURL` 绘制时的类型使用 `image/jpeg`进行保存


## 实现效果
![weche-qrcode.gif](../images/wechat-qrcode.gif)

## 源码地址