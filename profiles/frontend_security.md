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

