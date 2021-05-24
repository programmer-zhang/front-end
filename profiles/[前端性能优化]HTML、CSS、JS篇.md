> 今天给大家带来前端面试必会问到的前端性能优化问题，暂定分三期给大家带来，第一期讲述基本的代码优化，后续还有网络传输层优化和页面加载速度优化，敬请期待，也欢迎点击查看原文了解更多前端小知识。

> 文章建议阅读时间：3分钟

## 阅读本文您将收获：
* 性能优化的整体思路
* 在HTML、CSS、JavaScript层级的性能优化

## 为什么要进行性能优化
* 用户: 提升用户体验，改善页面性能
* 开发者: 体现公司意志和开发人员技能

## 性能优化的总体方向
* 高效 ：合理安排资源
* 快速 ：减少等待时间
* 标准 ：
	* 首次有效绘制（`First Meaningful Paint`，简称FMP，当主要内容呈现在页面上）
	* 英雄渲染时间（`Hero Rendering Times`，度量用户体验的新指标，当用户最关心的内容渲染完成）
	* 可交互时间（`Time to Interactive`，简称TTI，指页面布局已经稳定，关键的页面字体是可见的，并且主进程可用于处理用户输入，基本上用户可以点击UI并与其交互）
	* 输入响应（`Input responsiveness`，界面响应用户输入所需的时间）
	* 感知速度指数（`Perceptual Speed Index`，简称PSI，测量页面在加载过程中视觉上的变化速度，分数越低越好）

## 优化方向
### HTML/CSS优化
* 能用html/css解决的问题就不要用js
	* 更快的开发速度，更小的维护成本
	* 适用场景
	
	```
	//导航高亮
	nav li {
		opacity: 0.5;
	}
	nav li:hover {
		opacity: 1;
	}
	```
	```
	//鼠标悬停显示模块
	.menu {
		display: none;
	}
	.nav:hover + .menu {
		display: inline-block;
	}
	.menu:before {
		content: '';
		position: absolute;
		top: -20px;
		left: 0px;
		width: 100%;
		height: 20px;
	}
	```
	* 自定义radio/checkbox样式
	
	```
	input[type=checkbox]{}
	input[type=checkbox]:checked{}
	```
	* 巧用css伪类，合理使用原生选择器，如：`:focus`、`@media`、`input[type=email]:invalid`
	* 使用sass、scss等预编译器
	
* 优化HTML标签
	* 文字`<p>``<h1>`减少css代码
	* 表单`<form>`
	* 列表`<ol>``<ul>`
	* 图片`<img>``<picture>`
	* 链接`<a>``<button>`
	* 根据情况使用input type值
	* 使用HTML5语义化标签
	
	```
	//一个语义化的网页布局
	<nav></nav>
	<header></header>
	<main>
		<section></section>
		<section></section>
	</main>
	<footer></footer>
	```
* 不滥用高消耗的样式
	* `box-shadow`、`border-radius`、`float`需要浏览器进行大量的计算，应减少使用
* 选择器合并
	* 把有共同的属性内容的一系列选择器组合到一起，能压缩空间和资源开销
* 0值去单位
	* 对于值为0的属性，尽量不要加单位，增加兼容性

### JS优化
* 减少前端代码耦合
	* 使用传参的方法减少耦合
	* 利用策略模式抽离公共组件、参数、封装请求
* JS书写优化
	* 按照强类型风格去写代码，指明变量类型和返回类型
	* 字面量与局部变量的访问速度最快，数组元素和对象成员相对较慢
	
	```
	//bad
	let num,
		str,
		obj;
	//good
	let num = 0;
		str = '',
		obj = null;
		
	//bad
	getPrice:function(price){
		if (price < 0) {
			return false;
		}else {
			return price * 10
		}
	}
	//good
	getPrice:function(price){
		if (price < 0) {
			return -1;
		}else {
			return price * 10
		}
	}
	//类型确定，解析器不会去做一些额外的的工作，类型不确定的情况下回发生优化回滚
	//优化回滚：编译器已经编译完成函数，类型变化导致回滚到通用状态，重新生成函数
	```
	
	* 减少作用域查找
		* 尽量少的不要让代码暴露在全局作用域下，变量从局部作用域到全局作用域的搜索过程越长速度越慢
	
	```
	//bad
	<script>
		var map = document.querySelector('#imap');
		map.style.height = '10px';
	</script>
	//good
	<script>
		!function() {
			var map = document.querySelector('#imap');
			map.style.height = '10px';
		}
	</script>
	```
	
	* 对象嵌套的越深，读取速度就越慢
		* 无意义的对象嵌套拖累读取速度
	* 避免 == 的使用
		* 确定类型的情况下直接使用 ===
	* 合并表达式
		* 用三目运算符代替简单的if-else
	
	```
	//bad
	function getPrice(count){
		if(count < 0){
			return -1;
		}else {
			return count * 100;
		}
	}
	//good
	function getPrice(count){
		return count <0 ? return -1 : count * 100;
	}
	//在进行代码压缩的时候，即时书写的是if-else，压缩工具也会帮你把它改成三目运算符的形式
	```
* 使用ES6简化代码
	* 使用箭头函数取代小函数
	
	```
	var num = [4,6,8,3,1,0]
	//bad
	num.sort(function (a,b){
		return a-b;
	})
	//good
	num.sort(a,b => a-b);
	```
	* 使用ES6的class
	
	```
	class Person {
		constructor(name, age) {
			this.name = name;
			this.age = age;
		}
		addAge() {
			this.age++;
		}
		setName(name) {
			this.name = name;
		}
	}
	```
	* 字符串拼接
	
	```
	let url = `/list.html?page=${page}&type=${type}`;
	```
	* 块级作用域变量，使用let代替var