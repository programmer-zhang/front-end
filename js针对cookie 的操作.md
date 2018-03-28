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