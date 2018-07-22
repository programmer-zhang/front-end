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