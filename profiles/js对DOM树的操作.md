## js操作DOM树和获取DOM树的高度以及宽度
### 获取DOM节点
* 通过ID获取DOM树 document.getElementById
* 通过class获取
	* element.getElementsByClassName // ie9+ 
	* element.querySelectorAll // ie8 
* 通过TagName获取	
	* element.getElementsByTagName
* 按照属性查询
	* element.querySelector //ie8+ 
	* element.querySelectorAll() //ie8+ 
	* querySelector返回返回的是单个DOM元素，querySelectorAll返回NodeList
* 获取父元素
	* element.parentNode
* 获取子元素
	* element.childNodes
* 获取兄弟节点
	* 获取前面的兄弟节点 element.previousSibling
	* 获取后面的兄弟节点 element.nextSiblin

### 操作DOM
* 创建DOM 
	* document.createElement(tagName)
* 新增DOM 添加到节点的子节点的最后 
	* paranetElement.appendChild(child)
* 添加到节点的前面 
	* paranetElement.insertBefore(newElement, Element)

### 修改DOM
* 修改DOM的文案 
	* element.innerHTML
* 修改css
	* element.style.cssAttribute
* 修改属性
	* element.setAttribute() 
	* element.removeAttribute() 
	* element.className
* 删除DOM
	* paranetElement.removeChild(element)
* 清空子节点
	* 没有专门的函数去操作，需要自己去遍历操作
	
	```
	var element = document.getElementById("top");
	while (element.firstChild) {
	    element.removeChild(element.firstChild);
	}
	```

### javascript中获取dom元素高度和宽度的方法如下：

* 网页可见区域宽： document.body.clientWidth
* 网页可见区域高： document.body.clientHeight
* 网页可见区域宽： document.body.offsetWidth (包括边线的宽)
* 网页可见区域高： document.body.offsetHeight (包括边线的高)
* 网页正文全文宽： document.body.scrollWidth
* 网页正文全文高： document.body.scrollHeight
* 网页被卷去的高： document.body.scrollTop
* 网页被卷去的左： document.body.scrollLeft

### 对应的dom元素的宽高有以下几个常用的：

* 元素的实际高度：document.getElementById("div").offsetHeight
* 元素的实际宽度：document.getElementById("div").offsetWidth
* 元素的实际距离左边界的距离：document.getElementById("div").offsetLeft
* 元素的实际距离上边界的距离：document.getElementById("div").offsetTop