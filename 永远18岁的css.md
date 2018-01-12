### oblique字体和italic字体在css样式中的差别
* 区别
	* italic: 指的是一种单独的字体风格，对每个字母的结构有一些小改动，来反映外观的变化,不一定每种字体都有这种风格
	* oblique: 指的是将正常竖直文本倾斜
* 使用
	* italic：斜体，对于没有斜体变量的特殊字体，将应用oblique
	* oblique：倾斜的字体

### 如何让一段文字自动换行
#### 第一种方案（适合webkit内核的浏览器或移动端浏览器）
* HTML
	* 使用&lt;pre&gt;&lt;/pre&gt;包裹要换行的内容
* css
	* 添加css

```  
white-space: pre-wrap!important;  
word-wrap: break-word!important;  
*white-space:normal!important;  
```

### 多行文本溢出显示省略号
* 一个不规范的css属性 ：-webkit-line-clamp 
	* 限制在一个块元素显示的文本的行数，为了实现该效果，需要组合其他webkit属性
* 相关属性
	* display: -webkit-box; 必须结合的属性 ，将对象作为弹性伸缩盒子模型显示  
	* -webkit-box-orient 必须结合的属性 ，设置或检索伸缩盒对象的子元素的排列方式 。
	* text-overflow，可以用来多行文本的情况下，用省略号“...”隐藏超出范围的文本 。
* 完整css

```
  overflow : hidden;
  text-overflow: ellipsis;
  display: -webkit-box;
  -webkit-line-clamp: 2;
  -webkit-box-orient: vertical;
```
#### 另一个跨浏览器解决方案
* 设置相对定位的容器高度，用包含省略号（。。。）的元素模拟实现

```
p { 
	position:relative;
	line-height:1.4em;
	/* 3 times the line-height to show 3 lines */
	height:4.2em;
	overflow:hidden;
	}
p::after {
	content:"...";
	font-weight:bold;
    position:absolute;
    bottom:0;
    right:0;
    padding:0 20px 1px 45px;
    background:url(http://newimg88.b0.upaiyun.com/newimg88/2014/09/ellipsis_bg.png) repeat-y;
}
```
* 注：
	1. height高度真好是line-height的3倍
	2. 结束的省略好用了半透明的png做了减淡的效果，或者设置背景颜色；
	3. IE6-7不显示content内容，所以要兼容IE6-7可以是在内容中加入一个标签，比如用`<span class="line-clamp">...</span>`去模拟；
	4. 要支持IE8，需要将::after替换成:after；