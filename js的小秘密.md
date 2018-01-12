### js 除法 取整
 
* 丢弃小数部分,保留整数部分
	* js:parseInt(7/2)
* 向上取整,有小数就整数部分加1
	* js: Math.ceil(7/2)
* 四舍五入.
	* js: Math.round(7/2)
* 向下取整
	* js: Math.floor(7/2)
* 注：都是JS内置对象

### HTML input 标签输入限制
* 文本框只能输入数字代码(小数点也不能输入) 

```
<input onkeyup="this.value=this.value.replace(/\D/g,'')"  
onafterpaste="this.value=this.value.replace(/\D/g,'')"> 
```

* 只能输入数字,能输小数点. 

```
<input onkeyup="if(isNaN(value))execCommand('undo')" onafterpaste="if(isNaN(value))execCommand('undo')"> 
<input name=txt1 onchange="
	if(/\D/.test(this.value)){
	alert('只能输入数字');
	this.value='';}"> 
```

* 数字和小数点方法二 

```
<input type=text tvalue="" ovalue="" onkeypress="
	if(!this.value.match(/^[\+\-]?\d*?\.?\d*?$/))this.value=this.t_value;
		else this.tvalue=this.value;
	if(this.value.match(/^(?:[\+\-]?\d+(?:\.\d+)?)?$/))this.ovalue=this.value" 
		onkeyup="if(!this.value.match(/^[\+\-]?\d*?\.?\d*?$/))this.value=this.t_value;
		else this.tvalue=this.value;
	if(this.value.match(/^(?:[\+\-]?\d+(?:\.\d+)?)?$/))this.ovalue=this.value" 
		onblur="if(!this.value.match(/^(?:[\+\-]?\d+(?:\.\d+)?|\.\d*?)?$/))this.value=this.o_value;
		else{if(this.value.match(/^\.\d+$/))this.value=0+this.value;
	if(this.value.match(/^\.$/))this.value=0;this.ovalue=this.value}">
```

* 只能输入字母和汉字 

```
<input onkeyup="value=value.replace(/[\d]/g,'') "onbeforepaste="
		clipboardData.setData('text',clipboardData.getData('text').replace(/[\d]/g,''))" 
		maxlength=10 name="Numbers"> 
```

* 只能输入英文字母和数字,不能输入中文

``` 
<input onkeyup="value=value.replace(/[^\w\.\/]/ig,'')"> 
```

* 只能输入数字和英文

```
<font color="Red">chun</font> 
<input onKeyUp="value=value.replace(/[^\d|chun]/g,'')"> 
```

* 小数点后只能有最多两位(数字,中文都可输入),不能输入字母和运算符号: 

```
<input onKeyPress="
	if((event.keyCode<48 || event.keyCode>57) && event.keyCode!=46 || /\.\d\d$/.test(value))
	event.returnValue=false"> 
```
* 小数点后只能有最多两位(数字,字母,中文都可输入),可以输入运算符号:
 
```
<input onkeyup="this.value=this.value.replace(/^(\-)*(\d+)\.(\d\d).*$/,'$1$2.$3')"> 
```

### JS判断字符串是不是全是空格

```
var test = "   \n   ";
//var test = "      ";
if(test.match(/^\s+$/)){
    console.log("all space or \\n")
}
if(test.match(/^[ ]+$/)){
    console.log("all space")
}
if(test.match(/^[ ]*$/)){
    console.log("all space or empty")
}
if(test.match(/^\s*$/)){
    console.log("all space or \\n or empty")
}
```
