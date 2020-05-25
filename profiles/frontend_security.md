# 前端安全
## 阅读本文您将收获
* 前端攻击的类型


## xss攻击
* xss攻击又叫做跨站脚本攻击，主要是用户输入或通过其他方式，向我们的代码中注入了一下其他的js，而我们又没有做任何防范，去执行了这段js。

* 可能用户会写一个死循环，将我们的页面给弄崩了，但是也有可能通过这种方式，来获取我们的cookie，从而回去登陆态等信息

* xss攻击从来源可分为反射型和存储型
* 反射型

```
<!DOCTYPE html>
<html lang="en">
<head>
	<meta charset="UTF-8">
	<title>反射型</title>
</head>
<body>
	<div id="test"></div>
	<script>
		var $test = document.querySelector('#test');
		$test.innerHTML = window.location.hash
	</script>
</body>
</html>
```

* 在IE浏览器去访问该页面并在地址后面加上 `index.html#<img src="404.html" onerror="alert('xss')" />`

* 这时页面一打开就会有个xss的弹窗，这就是最简单的反射型攻击
当然可能会觉得这样没有任何作用，但是修改一下代码

```
<!DOCTYPE html>
<html lang="en">
<head>
	<meta charset="UTF-8">
	<title>反射型</title>
</head>
<body>
	<div id="test"></div>
	<script>
		// 先向页面的cookie存储一个name=1的信息
		document.cookie = "name=1"
		var $test = document.querySelector('#test');;
		$test.innerHTML = window.location.hash
	</script>
</body>
</html>
```

此时打开的地址修改为 `index.html#<img src="404.html" onerror="alert(document.cookie)" />`

这里就会发现弹窗内容为我们存取的cookie。

注意：1.这里必须用IE打开这个链接，因为chrome和safari等浏览器，会主动将url里的一下字符串进行encode，保证了一定的安全性。 2.为什么我们这里用img的onerror来注入脚本呢？而不是直接用script标签来执行，我们修改一下访问的地址index.html#<script>alert(document.cookie)</script>，这时会发现，页面并没有执行这段代码，但是这段代码已经注入到了#test标签中了。所以，一般通过img的onerror来注入是最有效的方法

## 存储型
* 将xss代码发送到了服务器，在前端请求数据时，将xss代码发送给了前端

```
<!DOCTYPE html>
<html lang="en">
<head>
	<meta charset="UTF-8">
	<title>存储型</title>
</head>
<body>
	<div id="test"></div>
	<script>
		// 先向页面的cookie存储一个name=1的信息
		document.cookie = "name=1"
		// 这里假设是请求了后台的接口 response是我们请求回来的数据
		var response = '<img src="404.html" onerror="alert(document.cookie)"'
		var $test = document.querySelector('#test');;
		$test.innerHTML = response
	</script>
</body>
</html>
```

这里最常见的情况就是一个富文本编辑器下，由用户输入了一串xss代码，存储在了服务器中，我们在展示用户输入内容时，没有做防范处理。

```
<!DOCTYPE html>
<html lang="en">
<head>
	<meta charset="UTF-8">
	<title>富文本</title>
</head>
<body>
	<div id="test"></div>
	<textarea name="" id="" cols="30" rows="10"></textarea>
	<button onclick="submit()">提交</button>
</body>
</html>
<script>
function submit() {
	var $test = document.querySelector('#test');
	$test.innerHTML = document.querySelector('textarea').value
}
</script>
```

## 防范手段
### encode
* encode也分为html的encode和js的encode

* html的encode： 就是将一些有特殊意义的字符串进行替换，比如：

```
& => &amp;
" => &quot;
' => &#39;
< => &lt;
> => &gt;
```

* 通过这些字符的替换，之前我们输入的 `<img src="null" onerror="alert()">` 就被encode成了`&lt;img src=&quot;null&quot; onerror=&quot;alert()&quot;&gt;`

* js的encode： 使用 '\' 对特殊字符进行转义，除数字字母之外，小于127的字符编码使用16进制 '\xHH' 的方式进行编码，大于用unicode（非常严格模式）

* 用 '\' 对特殊字符进行转义,这个可能比较好理解，因为将一下，比如'，"这些字符转译为',"就可以使得js变为一个字符串，而不是一个可执行的js代码了，那为什么还需要进行16进制转换和unicode转换呢？这样做是为了预防一下隐藏字符，比如换行符可能会对js代码进行换行

```
//使用“\”对特殊字符进行转义，除数字字母之外，小于127使用16进制“\xHH”的方式进行编码，大于用unicode（非常严格模式）。
var JavaScriptEncode = function(str){  
    var hex=new Array('0','1','2','3','4','5','6','7','8','9','a','b','c','d','e','f');
    function changeTo16Hex(charCode){
        return "\\x" + charCode.charCodeAt(0).toString(16);
    }
    function encodeCharx(original) {
        var found = true;
        var thecharchar = original.charAt(0);
        var thechar = original.charCodeAt(0);
        switch(thecharchar) {
            case '\n': return "\\n"; break; //newline
            case '\r': return "\\r"; break; //Carriage return
            case '\'': return "\\'"; break;
            case '"': return "\\\""; break;
            case '\&': return "\\&"; break;
            case '\\': return "\\\\"; break;
            case '\t': return "\\t"; break;
            case '\b': return "\\b"; break;
            case '\f': return "\\f"; break;
            case '/': return "\\x2F"; break;
            case '<': return "\\x3C"; break;
            case '>': return "\\x3E"; break;
            default:
                found=false;
                break;
        }
        if(!found){
            if(thechar > 47 && thechar < 58){ //数字
                return original;
            }
            if(thechar > 64 && thechar < 91){ //大写字母
                return original;
            }
            if(thechar > 96 && thechar < 123){ //小写字母
                return original;
            }        
            if(thechar>127) { //大于127用unicode
                var c = thechar;
                var a4 = c%16;
                c = Math.floor(c/16); 
                var a3 = c%16;
                c = Math.floor(c/16);
                var a2 = c%16;
                c = Math.floor(c/16);
                var a1 = c%16;
                return "\\u"+hex[a1]+hex[a2]+hex[a3]+hex[a4]+"";        
            }
            else {
                return changeTo16Hex(original);
            }
        }
    }     

    var preescape = str;
    var escaped = "";
    var i=0;
    for(i=0; i < preescape.length; i++){
        escaped = escaped + encodeCharx(preescape.charAt(i));
    }
    return escaped;
}
```

### 对于富文本的防范：filter
* 因为富文本是比较特殊的，在富文本中输入标签，我们需要展示出来，所以我们不能用之前的html的encode方法来执行。所以我们就得用一个叫白名单过滤的方式来防范。

* 原理就是：首先列举一下比较合法的标签，称为白名单，这些标签是不会对页面进行攻击的。之后对用户输入的内容进行白名单过滤。

```
<!DOCTYPE html>
<html lang="en">
<head>
	<meta charset="UTF-8">
	<title>富文本</title>
</head>
<body>
	<div id="test"></div>
	<textarea name="" id="" cols="30" rows="10"></textarea>
	<button onclick="submit()">提交</button>
</body>
</html>
<!-- xss_filter.js是一个白名单过滤库 -->
<script src="./xss_filter.js"></script>
<script>
function submit() {
	var $test = document.querySelector('#test');
	$test.innerHTML = filterXSS(document.querySelector('textarea').value)
}
</script>
```

* 此时用户如果提交 `<img src="null" onerror="alert()">` 就会被过滤成 `<img src="">`
这样就有效的防范了用户输入的xss代码

* 注意： 当遇到想后台提交数据的情况，我们应该在用户提交的时候就进行过滤和encode呢？还是展示的时候处理呢？ 我们应当考虑到提交代码后会有个多端展示的问题，可能我们web端需要进行这些处理，但是移动端展示的时候就不需要这些处理，所以我们应当在展示的时候进行处理，而不是录入的时候处理


## CSRF– 跨站伪造请求

