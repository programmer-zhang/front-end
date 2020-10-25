# tabindex的作用
> HTML 的 tabindex 属性开发过程中一般不会使用到，最近开发中有个需求用到了，便总结了一下。本篇文章同时收录【前端知识点】中，[链接直达](https://github.com/programmer-zhang/front-end)

> 按照惯例，先欣赏一波[官方定义](http://www.w3school.com.cn/tags/att_standard_tabindex.asp)

> Safari不支持！

## 阅读本文您将收获
* tabindex的作用
* tabindex的使用
* 如何利用 tabindex 创造更好的用户体验

## tabindex的作用
* **元素是否能聚焦**：通过键盘这类输入设备，或者通过 `JS focus()` 方法

* **元素什么时候能聚焦**：在用户通过键盘与页面交互时

* **通俗来说**：就是当用户使用键盘时，tabindex用来定位html元素,即使用tab键时焦点的顺序。

## tabindex的范围
### tabindex 有三个值：0，-N(通常是-1)，N(正值)
* `tabindex=0`，该元素可以用tab键获取焦点，且访问的顺序是按照元素在文档中的顺序来focus，即使采用了浮动改变了页面中显示的顺序，依然是按照html文档中的顺序来定位。

* `tabindex=-1`，该元素用tab键获取不到焦点，但是可以通过js获取，这样就便于我们通过js设置上下左右键的响应事件来focus。

* `tabindex>=1`，该元素可以用tab键获取焦点，而且优先级大于`tabindex=0`；不过在`tabindex>=1`时，数字越小，越先定位到。