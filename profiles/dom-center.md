# DOM元素 居中的多种实现方式

> 前端开发过程中，经常会碰到需要 DOM 元素居中的场景，例如行内文字居中，部分 DOM 节点在父元素中垂直水平居中等等，面对不同的情况，居中的实现方式有很多种，今天就让我们来重温一下 DOM元素  居中的多种实现方式。

> 居中方式也是面试过程中经常会被问到的前端基础面试题，面试官有时仅仅会问 居中的方式，但是请不要忘记居中也是分为多种情况的哦，面对这种问题，一定要回答的思路清晰且准确。

## 阅读本文您将收获
* 水平居中的实现方式
* 垂直居中的实现方式

## 水平居中
### 行内元素

```
.parent {
	text-align: center;
}
```

### 块级元素

#### 基础居中方式

```
.son {
	margin: 0 auto;
}
```

#### Flex 弹性布局方式
* 这是大家在开发过程中最常用的方式之一，也是最快速实现居中的方式，针对高度固定与不固定的 DOM 元素均可以使用这种方式，目前 Flex 布局的兼容性也很好，成为大家解决居中问题的首选方式。
* 但是这种方式也会存在一定的问题，如果在开发中引入一些未使用 Flex 布局的组件，就会对页面的排版造成影响，需要进行样式覆盖，所以大家在开发的过程中也可以根据自身需要选择合适的方式。

```
.parent {
	display: flex;
	justify-content: center;
}
```

#### 绝对定位方式
* transform 位移方式
	* 采用将DOM节点的基础计算位置调整到父节点中央，然后通过反向移动子节点自身宽度一半的方式达到居中的目的。

```
.parent {
	position: relative;
}
.son {
	position: absolute;
	left: 50%;
	transform: translateX(-50%);
}
```

* 负margin 位移方式
	* 原理与transform位移方式相同。

```
.parent {
	position: relative;
}
.son {
	position: absolute;
	width: xx;
	left: 50%;
	margin-left: -.5*xx
}
```

* 水平零值方式

```
.parent {
	position: relative;
}
.son {
	position: absolute;
	width: xx;
	left: 0;
	right: 0;
}
```

## 垂直居中
### 行内元素
* 单行文本的垂直居中只需要将文本节点的 `父节点高度 === 子节点行高` 即可。

```
.parent {
	height: single_line_height;
}	
.son {
	line-height: single_line_height;
}
```
### 块级元素
#### 行内块级元素
* 使用display: inline-block; vertical-align: middle; 和一个伪元素让内容块处于容器中央。

```
.parent::after{
	content:'';
	height:100%;
	display:inline-block;
	vertical-align:middle;
}
.son{
	display:inline-block;
	vertical-align:middle;
}
```

#### table 布局
* 这种方式虽然可以解决问题，但是鉴于 `table` 布局较少使用而且对SEO不友好，不建议使用这种方式。

```
.parent {
	display: table;
}
.son {
	display: table-cell;
	vertical-align: middle;
}
```

#### Flex 弹性布局方式

```
.parent {
	display: flex;
	align-items: center;
}
```

#### 绝对定位方式
* transform 位移方式
	* 原理与水平居中的 transform 位移方式相同

```
.son {
    position: absolute;
    top: 50%;
    transform: translateY( -50%);
}
```

* 负 margin 位移方式
	* 原理与水平居中的负 margin 位移方式相同

```
.son {
	position: absolute;
	height: xx;
	top: 50%;
	margin-left: -.5*xx
}
```

* 垂直零值方式

```
.son {
	position: absolute;
	top: 0;
	bottom: 0;
	margin: auto 0;
}
```