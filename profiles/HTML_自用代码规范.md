# 文件规范
* 命名
	* 语义化命名：单词必须是有意义的，且符合当前使用的场景 
	* 全部小写
	* 单词之间用 - 隔开
	* 层级关系 `.` 隔开
	
# 代码结构
* 缩进 一个Tab（四个空格）
* 代码对齐
* 相邻span标签会产生异常间隙
	
# JS规范
## 命名规范
* 标识符必须以字母、下划线、美元符号开始，不得以数字开头
* 类名首字母大写，遵从驼峰形式
* 方法名、参数名、变量名都统一首字母小写，遵从驼峰形式

## 代码书写格式
* 大括号的使用约定如下：
	* 左大括号前不换行
	* 左大括号后换行
	* 右大括号前换行
	* 右大括号后还有else等代码则不换行，表示终止的右大括号后需要换行
	* 左小括号和字符之间不出现空格，右小括号和字符之间也不出现空格
	* 如： if空格(a空格==空格b)
* if/for/while/switch 等保留字与括号之间都需要加空格
	* 如：for空格(i = 0; i < 5; i++)
* 任何二目、三目运算符的左右两边都需要加一个空格。包括赋值运算符=、逻辑运算符&&、加减乘除符号等。
* 采用4个空格缩进。如果使用tab缩进，设置1个tab为4个空格
* 注释的双斜线和注释内容之间有且仅有一个空格
* 换行时需要遵循如下原则：
	* 第二行相对第一行缩进4个空格，从第三行开始，不再继续缩进
* 方法参数在定义和传入时，多个参数逗号后面需要加空格
	* 如： method("a",空格"b",空格"c");
* 每个独立语句结束后换行，如if、catch、while

## 声明变量
* 使用 var 来声明变量。不使用var将导致产生全局变量，污染全局命名空间。
* 在作用域顶部声明变量。这将帮你避免变量声明提升相关的问题。
* 比较运算符
* 优先使用 === 和 !== 而不是 == 和 !=.
* 条件表达式例如 if 语句总是遵守下面的规则：
	* 对象 被计算为 true
	* Undefined 被计算为 false
	* Null 被计算为 false
	* 布尔值 被计算为 布尔的值
* 数字 如果是 +0、-0 或 NaN 被计算为 false，否则为 true
* 字符串 如果是空字符串 '' 被计算为 false，否则为 true

## 注释
* 使用 /** ... */ 作为多行注释。包含描述、指定所有参数和返回值的类型和值。
* 使用 // 作为单行注释。

## 字符串
* 使用单引号优先于双引号，如创建一个包含html的字符串：

	```
	var msg = 'This is some <div class=”aff”></div>';
	```
	
## 不要使用with。
* 因为with的对象，可能会与局部变量产生冲突。

## for-in循环。
* 只用于 object/map/hash的遍历，对数组用for-in循环有时会出错。因为它遍历的是所有出现在对象及其原型链的键值，而遍历数组通常用最普通的 for 循环。


# HTML规范
## 代码风格
### 缩进与换行
* 使用 4 个空格做为一个缩进层级，不允许使用 2 个空格 或 tab 字符。
* 每行控制字符，非必要情况下应换行

### 命名
* class命名
	* class 必须单词全字母小写，单词间以 - 分隔。
	* class 设计JS绑定的必须以'j-'开头，如'.j-tabBar'。
	* class 必须代表相应模块或部件的内容或功能，不得以样式信息进行命名。
* id命名
	* 元素 id 必须保证页面唯一。
	* id涉及JS绑定的必须以'j-'开头，如'#j-tabNav'。
	* id 建议单词全字母小写，单词间以 - 分隔。同项目必须保持风格一致。
* id、class 命名，在避免冲突并描述清楚的前提下尽可能短。

### 标签
* 标签名必须使用小写字母。

* 对于无需自闭合的标签，不允许自闭合。
	* 常见无需自闭合标签有 input、br、img、hr 等。
	
	 ```
	<!-- good -->
	<input type="text" name="title">  
	<!-- bad -->
	<input type="text" name="title" />
	```

* 对 HTML5 中规定允许省略的闭合标签，不允许省略闭合标签。

	 ```
	<!-- good -->
	<ul>
		<li>first</li>
	    <li>second</li>
	</ul>
	<!-- bad -->
	<ul>
	    <li>first
	    <li>second
	</ul>
	```
	
* HTML 标签的使用应该遵循标签的语义。
	
	```
	p - 段落
	h1,h2,h3,h4,h5,h6 - 层级标题
	ins - 插入
	del - 删除
	abbr - 缩写
	code - 代码标识
	cite - 引述来源作品的标题
	q - 引用
	blockquote - 一段或长篇引用
	ul - 无序列表
	ol - 有序列表
	dl,dt,dd - 定义列表
	```
* 标签的使用应尽量简洁，减少不必要的标签。

### 属性
* 属性名必须使用小写字母。
* 属性值必须用双引号包围。
* 布尔类型的属性，建议不添加属性值。

	```
	<input type="text" disabled>
	<input type="checkbox" value="1" checked>
	```
	
## 通用
### DOCTYPE
* 使用 HTML5 的 doctype 来启用标准模式，建议使用大写的 DOCTYPE。

```
<!DOCTYPE html>
```

### 编码
* 页面必须使用精简形式，明确指定字符编码。指定字符编码的 meta 必须是 head 的第一个直接子元素。
* HTML 文件使用无 BOM 的 UTF-8 编码。

### CSS 和 JavaScript 引入
* 展现定义放置于外部 CSS 中，行为定义放置于外部 JavaScript 中。
	* 结构-样式-行为的代码分离，对于提高代码的可阅读性和维护性都有好处。
* 在 head 中引入页面需要的所有 CSS 资源。
* JavaScript 应当放在页面末尾，或采用异步加载。

## head
### title
* 页面必须包含 title 标签声明标题。
* title 必须作为 head 的直接子元素，并紧随 charset 声明之后。

### favicon
* 保证 favicon 可访问。
* 在未指定 favicon 时，大多数浏览器会请求 Web Server 根目录下的 favicon.ico 。为了保证 favicon 可访问，避免 404，必须遵循以下两种方法之一：
	* 在 Web Server 根目录放置 favicon.ico 文件。
	* 使用 link 指定 favicon。

### viewport
* 若页面欲对移动设备友好，需指定页面的 viewport。

## 图片
* 禁止 img 的 src 取值为空。延迟加载的图片也要增加默认的 src。
* 避免为 img 添加不必要的 title 属性。
* 为重要图片添加 alt 属性。
* 添加 width 和 height 属性，以避免页面抖动。

## 表单
* 在针对移动设备开发的页面时，根据内容类型指定输入框的 type 属性。

## 多媒体
* 当在现代浏览器中使用 audio 以及 video 标签来播放音频、视频时，应当注意格式。
* 在支持 HTML5 的浏览器中优先使用 audio 和 video 标签来定义音视频元素。
	
# css规范
## 代码风格
### 命名、缩进与换行
* 语义化命名：单词必须是有意义的，且符合当前使用的场景 
* 全部小写
* 单词之间用 - 隔开
* 模块：命名空间-模块名-其它描述
* 子模块：模块名-子模块名-其它描述
* 缩进 一个Tab（四个空格）
* css属性和值之间加一个空格，例如：display: flex;
* sass嵌套一定要缩进且正确缩进
* 非必须情况下勿使用内联样式设置属性
* 层级最多三级
* css大括号各占一行，属性呈竖向排版。

## 通用
* 合理使用CSS缩写属性
* 去掉小数点前的“0”
* 16进制颜色代码缩写
* 合理使用css注释
* 体现出层级性，例如：.detail .header{ ... }



## 属性声明顺序
* 为了保证更好的可读性，我们应该遵循以下顺序：
	* 定位：position | z-index | top | right | bottom | left | clip
	* 布局：display | float | clear | visibility | overflow | overflow-x | overflow-y
	* 尺寸：width | min-width | max-width | height | min-height | max-height
	* 外边距：margin | margin-top | margin-right | margin-bottom | margin-left
	* 内边距：padding | padding-top | padding-right | padding-bottom | padding-left
	* 边框：border | border-top | border-right | border-bottom | border-left | border-radius | box-shadow | border-image
	* 背景：background | background-color | background-image | background-repeat | background-attachment | background-position | background-origin | background-clip | background-size
	* 颜色：color | opacity
	* 字体：font | font-style | font-variant | font-weight | font-size | font-family
	* 文本：text-transform | white-space | word-break | word-wrap | overflow-wrap | text-align | word-spacing | letter-spacing | text-indent | vertical-align | line-height
	* 文本修饰：text-decoration | text-shadow
	* 书写模式：direction | unicode-bidi | writing-mode
	* 列表：list-style | list-style-image | list-style-position | list-style-type
	* 表格：table-layout | border-collapse | border-spacing | caption-side | empty-cells
	* 内容：content | counter-increment | counter-reset | quotes
	* 用户界面：appearance | text-overflow | outline | outline-width | outline-color | outline-style | outline-offset | cursor | zoom | box-sizing | resize | user-select
	* 多列：columns | column-width | column-count | column-gap | column-rule | column-rule-width| column-rule-style | column-rule-color | column-span | column-fill | column-break-before | column-break-after | column-break-inside
	* 伸缩盒：flex
	* 变换，过渡，动画：transform | transition | animation
