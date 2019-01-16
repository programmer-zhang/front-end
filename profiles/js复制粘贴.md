> 在业务需求中碰到主动协助用户进行复制粘贴的需求，需要通过js去主动复制变量从而协助用户进行相关操作

## 复制
### 通过原生js实现
* 复制可以通过document对象实现 `document.execCommand('copy')`
* 关于此语法的兼容情况可以[点击这里](https://developer.mozilla.org/zh-CN/docs/Web/API/Document/execCommand)
* 官方api中已经给出了一个示例，本文仅展示一个vue中的示例
* 思路：
	* 将变量动态绑定
	* 是指一个隐藏的输入框
	* 获取输入框的value
	* 调用官方api进行复制
	
```
//roles为需要复制的data
//HTML部分
<button @click="copyRoles">复制权限</button>
<input v-if="roles && roles.length>0" 
	id="clipBoard" 
	v-model="roles" 
	style="position: absolute; opacity: 0;">
</input>

//js部分
copyRoles() {
	let data = '';
	if(this.roles && this.roles.length > 0) {
		let spanSelect = document.querySelector('#clipBoard');
		spanSelect.select();
		if(document.execCommand('copy')) {
			document.execCommand('copy');
			console.log(`用户${this.formData.userName}的权限信息已复制成功`);
		} else {
			console.log('复制失败!');
		}
	} else {
		console.log('复制失败!');
	}
}
```

### 通过clipboard.js实现(官方api很详细，不做过多介绍)

## 粘贴
* 粘贴现在没有发现什么比较好的方案，不经过用户主动操作去进行粘贴操作，直接用js去操作粘贴板兼容性极差，建议考虑一种合理的方案去实现这个功能。简单来说就是：改需求！

### 原生js实现(兼容性极差，仅支持IE和FF浏览器)

* JavaScript 的clipboardData对象提供三个方法对粘贴板进行访问
	* clearData(sDataformat):删除剪贴板中指定格式的数据 
	* setData(sDataformat,sData):给剪贴板赋予指定格式的数据，返回true则操作成功 
	* getData(sDataformat):从剪贴板获取指定格式的数据
	
```
let text = "123"; 
if (!window.clipboardData.setData()) {
	return;
}
window.clipboardData.setData('Text', text) // 赋予 text 格式的数据 
alert("复制失败!"); 
text = window.clipboardData.getData('Text'); // 获取 text 格式的数据 
alert(text); 
window.clipboardData.clearData('Text'); // 清除 text 格式的数据 
text = window.clipboardData.getData('Text'); 
alert(text); 
```
### 一个婉转的方案
* 用户主动触发paste事件(三种方式)
	* 按下 CTRL + V
	* 从浏览器的编辑菜单中选择 "Paste（粘贴）"
	* 右击鼠标按钮在上下文菜单中选择 "Paste（粘贴）"
* 通过这三种方式，我们的解决方案主要有两个方面
	* 主动提示引导用户将剪贴板中的内容复制到input框，或者textarea中，从而获取值
	* `window.addEvent("paste",function(e){ });`通过监听事件，监听用户的粘贴操作，从而拿到想要的数据