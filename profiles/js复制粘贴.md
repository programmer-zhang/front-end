> 在业务需求中碰到主动协助用户进行复制粘贴的需求，需要通过js去主动复制变量从而协助用户进行相关操作

## 复制
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

## 粘贴