> 此文件主要留存日常代码书写过程中的 HTML&CSS 相关的报错与解决方案，部分方案参考自网络，如有侵权，请联系chinajnzhang@hotmail.com删除

### &lt;input type="number"&gt;maxlength失效
* 移动端页面为了方便用户交互通常将手机号输入框和数字输入框设置type属性，在用户触发fcous（）时手机键盘自动弹出数字键盘。
* 解决方案：修改为&lt;input
type="tel"&gt;可以起到同样的效果

### 解决图形验证码接口返回文件流图片
* 将图片按照相关格式转码即可
* `<img class="image-code" :src="'data:image/png;base64,'+imgCodeUrl"/>`

### 火狐浏览器去掉`type="number"的箭头`

```
input[type="number"] {
    -moz-appearance: textfield;
}
```

### 只能输入数字的正则表达式在火狐的兼容问题解决方法
* 加入了event.which  保证了火狐的兼容问题

```
<input alt="" class="" name='' type="text" onkeypress="return (/[\d.]/.test(String.fromCharCode(event.which||event.KeyCode)))"/>
```

### 设置两个DIV为display:inline-block出现上下错位问题
* 原因：
	* 同一行的行内元素对齐方式默认是底部对齐，即`vertical-align：baseline`
	* 对于内容为空的`inline-block`元素而言，该元素的基线就是它的`margin`底边缘，否则就是元素的内部最后一行内联元素的基线
* 解决方式：
	* `float`（考虑脱离流后后面元素不易控制，故不首选）
	* `vertical-align: top`

### input textarea 实现placeholder换行
* 当我们在使用`<input type="textarea">`时，`placeholder`属性需要设置换行时可以使用unicode字符`&#10`进行处理
`<input type="textarea" rows="5" placeholder="测试换行符&#10;这是第二行的placeholder">`