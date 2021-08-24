# DOM元素 居中的多种实现方式

> 前端开发过程中，经常会碰到需要 DOM 元素居中的场景，例如行内文字居中，部分 DOM 节点在父元素中垂直水平居中等等，面对不同的情况，居中的实现方式有很多种，今天就让我们来重温一下 DOM元素  居中的多种实现方式。

> 居中方式也是面试过程中经常会被问到的前端基础面试题，面试官有时仅仅会问 居中的方式，但是请不要忘记居中也是分为多种情况的哦，面对这种问题，一定要回答的思路清晰且准确。

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

#### Flex弹性布局方式

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

* margin 位移方式
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

* 左右空值方式

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
