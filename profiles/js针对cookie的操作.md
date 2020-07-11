## 阅读本文您将收获
* js 读取cookie操作
* js 删除cookie操作

## js读取cookie操作
* 方法一

```
let acookie=document.cookie.split("; ");
	//获取单个cookies
getck:function(sname){
	for(let i=0;i<acookie.length;i++){
		let arr=acookie[i].split("=");
		if(sname==arr[0]){
			if(arr.length>1){
				return unescape(arr[1]);
			}else{
			return "";
			}
		}
	}
	return "";
}
```
* 方法二

```
//获取指定名称的cookie的值
getcookie:function(objname) {
	let arrstr = document.cookie.split("; ");
	for(var i = 0;i < arrstr.length;i ++){
		var temp = arrstr[i].split("=");
		if(temp[0] == objname) {
		return unescape(temp[1]);
		}
	}
}
```
* 方法三

```
getcookie: function(cookiename) { 
	let cookiestring = document.cookie; 
	let start = cookiestring.indexof(cookiename + '='); 
	if (start == -1){   //找不到 
		return null; 
	}
	start += cookiename.length + 1; 
	let end = cookiestring.indexof(";", start); 
	if (end == -1){
	   return;
   }
   unescape(cookiestring.substring(start)); 
	return   
	unescape(cookiestring.substring(start, end)); 
}
```
* 方法四

```
readcookie: function(name) {   
	let cookievalue = "";   
	let search = name + "=";   
	if(document.cookie.length > 0){    
		offset = document.cookie.indexof(search);   
	   if (offset != -1) {    
	      offset += search.length;   
	      end = document.cookie.indexof(";", offset);   
	      if (end == -1) {
		      end = document.cookie.length;  
		   } 
	       cookievalue = unescape(document.cookie.substring(offset, end))   
	    }   
	}   
	return cookievalue;   
}
```

## js删除cookie

```
delCookie: function(name) {
	let exp = new Date();
	exp.setTime(exp.getTime() - 1);
	let cval=getCookie(name);
	if(cval!=null)
	document.cookie= name + "="+cval+";expires="+exp.toGMTString();
}
//使用示例
setCookie("name","hayden");
alert(getCookie("name"));
//如果需要设定自定义过期时间
//那么把上面的setCookie　函数换成下面两个函数就ok;
//程序代码
setCookie:function(name,value,time) {
	let strsec = getsec(time);
	let exp = new Date();
	exp.setTime(exp.getTime() + strsec*1);
	document.cookie = name + "="+ escape (value) + ";expires=" + exp.toGMTString();
}
getsec:function(str) {
	alert(str);
	let str1=str.substring(1,str.length)*1;
	let str2=str.substring(0,1);
	if (str2=="s") {
		return str1*1000;
	}else if (str2=="h"){
		return str1*60*60*1000;
	}else if (str2=="d") {
		return str1*24*60*60*1000;
	}
}
//这是有设定过期时间的使用示例：
//s20是代表20秒
//h是指小时，如12小时则是：h12
//d是天数，30天则：d30
setCookie("name","hayden","s20");
```