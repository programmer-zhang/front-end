## 为什么要进行性能优化
* 用户: 提升用户体验，改善页面性能
* 开发者: 体现公司意志和开发人员技能

## 性能优化的总体方向
* 高效 ：合理安排资源
* 快速 ：减少等待时间
* 标准 ：
	* 首次有效绘制（First Meaningful Paint，简称FMP，当主要内容呈现在页面上）
	* 英雄渲染时间（Hero Rendering Times，度量用户体验的新指标，当用户最关心的内容渲染完成）
	* 可交互时间（Time to Interactive，简称TTI，指页面布局已经稳定，关键的页面字体是可见的，并且主进程可用于处理用户输入，基本上用户可以点击UI并与其交互）
	* 输入响应（Input responsiveness，界面响应用户输入所需的时间）
	* 感知速度指数（Perceptual Speed Index，简称PSI，测量页面在加载过程中视觉上的变化速度，分数越低越好）

## 优化方向
### HTML/CSS优化
* 能用html/css解决的问题就不要用js
	* 更快的开发速度，更小的维护成本
	* eg:导航高亮、鼠标悬停
	
	```
	nav li {
		opacity: 0.5;
	}
	nav li:hover {
		opacity: 1;
	}
	```
	```
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
	```

	* 自定义样式与css伪类
		* 使用全局样式sass、scss,
		
		`:focus`
		`input[type=checkbox]:checked{}`
		`@media`
	
* 优化HTML标签
	* 文字`<p>``<h1>`减少css代码
	* 表单`<form>`
	* 列表`<ol>``<ul>`
	* 图片`<img>``<picture>`
	* 链接`<a>``<button>`
	* 根据情况使用input type值
	* 使用HTML5语义化标签
	
	```
	<nav></nav>
	<header></header>
	<main>
		<section></section>
		<section></section>
	</main>
	<footer></footer>
	```
* 不滥用高消耗的样式
	* box-shadow、border-radius、float需要浏览器进行大量的计算，应减少使用
* 选择器合并
	* 把有共同的属性内容的一系列选择器组合到一起，能压缩空间和资源开销
* 0值去单位
	* 对于为0的值，尽量不要加单位，增加兼容性


### js优化
* 减少前端代码耦合
	* 利用策略模式抽离公共组件、参数、封装请求
* js书写优化
	* 字面量与局部变量的访问速度最快，数组元素和对象成员相对较慢
	* 按照强类型风格去写代码，指明变量类型和返回类型
	
	```
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
	//类型确定，解析器不会去做一些额外的的工作
	//优化回滚：编译器已经编译完成函数，类型变化导致回滚到通用状态，重新生成函数
	```
	
	* 减少作用域查找，尽量少的不要让代码暴露在全局作用域下，变量从局部作用域到全局作用域的搜索过程越长速度越慢
	
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
	* 避免 == 的使用
		* 确定类型的情况下直接使用 ===
* 使用ES6简化代码