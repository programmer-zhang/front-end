按照惯例，先欣赏一波[官方定义](http://www.w3school.com.cn/tags/att_standard_tabindex.asp)
> Safari不支持！

## 作用
* 当使用键盘时，tabindex用来定位html元素,即使用tab键时焦点的顺序。
* tabIndex 有三个值：0，-1，n
	* tabindex=0，该元素可以用tab键获取焦点，且访问的顺序是按照元素在文档中的顺序来focus，即使采用了浮动改变了页面中显示的顺序，依然是按照html文档中的顺序来定位。

	* tabindex=-1，该元素用tab键获取不到焦点，但是可以通过js获取，这样就便于我们通过js设置上下左右键的响应事件来focus。

	* tabindex>=1，该元素可以用tab键获取焦点，而且优先级大于tabindex=0；不过在tabindex>=1时，数字越小，越先定位到。