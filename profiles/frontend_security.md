# 前端安全
> 我们不是要做一个能够解决问题的方案，而是要做一个能够'漂亮地'解决问题的方案------《白帽子讲web安全》

## 阅读本文您将收获
* 前端攻击的各种类型
* 前端攻击的防范措施

## 跨站脚本攻击(xss攻击)
* xss攻击又叫做跨站脚本攻击(cross site script)，也就是俗称的XSS攻击
* 主要是用户输入或通过其他方式，向我们的代码中注入了一下其他的js，而我们又没有做任何防范，去执行了这段js
* 可能破坏者会写一个死循环，将我们的页面给弄崩了；但是也可以通过这种方式，来获取我们的cookie，从而获取登陆态等信息，从而造成信息泄露

* xss攻击根据效果可分为反射型、存储型、DOM Based XSS

### 反射型(非持久型XSS攻击)
* 简单地把用户输入的数据“反射”给浏览器，黑客往往需要诱使用户“点击”一个恶意链接，才能攻击成功

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

注意：
1.这里必须用IE打开这个链接，因为chrome和safari等浏览器，会主动将url里的一下字符串进行encode，保证了一定的安全性
2.为什么我们这里用img的onerror来注入脚本呢？而不是直接用script标签来执行，我们修改一下访问的地址index.html#<script>alert(document.cookie)</script>，这时会发现，页面并没有执行这段代码，但是这段代码已经注入到了#test标签中了。所以，一般通过img的onerror来注入是最有效的方法

### 存储型(持久型XSS攻击)
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

* 这里最常见的情况就是一个富文本编辑器下，由用户输入了一串xss代码，存储在了服务器中，我们在展示用户输入内容时，没有做防范处理。

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

### DOM Based XSS
* 通过修改页面的DOM节点行成的XSS，称之为DOM Based XSS
* 并非按照“数据是否保存在服务器端”进行划分的，从效果上来说也是属于反射型XSS，单独将它化分为一个类型是因为发现它的安全专家提出了这种类型的XSS，出于历史原因，也就把它单独作为一个分类

```

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


## CSRF  跨站伪造请求
* CSRF就是利用你所在网站的登录的状态，悄悄提交各种信息， 是一种比xss还要恶劣很多的攻击。因为CSRF可以在我们不知情的情况下，利用我们登陆的账号信息，去模拟我们的行为，去执行一下操作，也就是所谓的钓鱼。比如我们在登陆某个论坛，但这个网站是个钓鱼网站，我们利用邮箱或者qq登陆后，它就可以拿到我们的登陆态，session和cookie信息。然后利用这些信息去模拟一个另外网站的请求，比如转账的请求。

![](../images/frontend-security.jpg)

* 我们点击进入了一个csrf这个页面里，我们以为我们只是在csrf中点击提交了1111这个信息，其实这个网站悄悄的把这些信息提交到了本地的csrf上了，而不是我们当前浏览的csrf.html中
```
// CSRF.html
<!DOCTYPE html>
<html lang="en">
<head>
	<meta charset="UTF-8">
	<title>csrf</title>
</head>
<body>
<iframe id="" name="csrf-form"></iframe>
<form target="csrf-form" method="post" action="http://127.0.0.1:3001/csrf">
	<input name="name" value="1111">
	<input type="submit" value="提交">
</form>
</body>
</html>
```
* 创建server.js 在命令行中输入node server.js
这是一个简单的服务，端口为3001，如果我们直接本地登陆localhost:3001会给我们本地注入一个cookie为login=1的登陆态
此时我们在访问csrf.html，在点击提交按钮的时候，会发现会把这个登陆态也提交上去。这就是一个典型的钓鱼网站。

### 防范措施
* 提交 method=Post 判断referer
* HTTP请求中有一个referer的报文头，用来指明当前流量的来源参考页。如果我们用post就可以将页面的referer带入，从而进行判断请求的来源是不是安全的网站。但是referer在本地起的服务中是没有的，直接请求页面也不会有。这就是为什么我们要用Post请求方式。直接请求页面，因为post请求是肯定会带入referer，但get有可能不会带referer。

* 利用Token
Token简单来说就是由后端生成的一个唯一的登陆态，并传给前端保存在前端，每次前端请求时都会携带着Token，后端会先去解析这个Token，看看是不是后台给我们的，已经是否登陆超时，如果校验通过了，才会同意接口请求
