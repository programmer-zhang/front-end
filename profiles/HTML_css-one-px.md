> 如何用CSS画一条一像素的细线，这个问题是很多开发者开发过程中常见的问题，也是前端同学在面试的时候常见的问题之一。

## 阅读本文您将收获到
* 为什么会造成一像素的细线视觉上会比一像素粗
* 如何精准地画一像素的线

## 明确几个概念
### 物理像素(设备像素)
* 显示屏幕的最小物理单位。
* 每个物理像素包含自己的颜色、高宽等，不可再细分。
* 物理像素是在设备出厂是设定的，设备一旦造出来就不会变大小和数量。官方在产品说明书上写的1920x1080就是说的物理像素。

### 设备独立像素
* 设备独立像素（也叫密度无关像素），可以认为是计算机坐标系统中得一个点，这个点代表一个可以由程序使用的虚拟像素（比如：css像素），有时我们也说成是逻辑像素，由相关系统转换为物理像素。
* 所以说，物理像素和设备独立像素之间存在着一定的对应关系，这就是接下来要说的 `设备像素比`。

### 设备像素比
* 设备像素比（简称dpr）定义了物理像素和设备独立像素的对应关系。
* 它的值可以按如下的公式的得到：

```
// 在某一方向上，x方向或者y方向
// 设备像素比(dpr) = 物理像素 / 逻辑像素(px)
```

## 视觉误差原因
* 因为屏幕的分辨率和浏览器的的分辨率存在换算关系，所以 `1像素的线` 在屏幕上会占用 `2个或者2个以上` 视觉像素，这点在移动端尤其明显。
* 由于占用视觉像素多的原因，导致渲染出来的这条细线在视觉上会比一像素要粗一些。

## 实现方法
### 使用伪类解决
* 使用 `tansform: scale(.5);` 将高度缩小一半
* 优点：所有场景都能满足
* 缺点：已经使用伪类的元素需要多层嵌套

> 注: `<input type="button">` 没有:before、:after伪元素

```
.box-one-px {
	position: relative;
}
.box-one-px:before {
	display: block;
	content: '';
	position: absolute;
	left: 0;
	top: 0;
	width: 200%;
	height: 1px;
	tansform: scale(.5);
	tansform-origin: 0 0;
	background: #f5f5f5;
}
```

### 使用 meta viewport 解决
* viewport 结合rem解决像素比问题，将细线的粗度自动按照比例进行缩放。

```
// 在devicePixelRatio = 2 的屏幕下设置meta
<meta name="viewport" content="initial-scale=0.5, maximum-scale=0.5, minimum-scale=0.5, user-scalable=no">
// 在devicePixelRatio = 3 的屏幕下设置meta
<meta name="viewport" content="initial-scale=0.3333333333333333, maximum-scale=0.3333333333333333, minimum-scale=0.3333333333333333, user-scalable=no">
```

### 使用 box-shadow 模拟边框
* 缺点：效果不理想，颜色也不好配置

```
.box-one-px {	
  box-shadow: inset 0px -1px 1px -1px #c8c7cc;
}
```

### 使用 border-image
* 需要自己制作一个 0.5像素 的线条作为背景图片
* 优点：图片可以用多种格式
* 缺点：大小，颜色更改不灵活，会增加不必要请求

```
.box-one-px {	
    border-width: 0 0 1px 0;	
    border-image: imageUrl 2 0 round;	
}
```

### 使用 背景渐变 linear-gradient
* 利用 `linear-gradient` 利用背景图片渐变，从有色到透明，默认方向从上到下，从0到50%的地方颜色是边框颜色，然后下边一半颜色就是透明了。然后设置背景宽度100%，高度是1px，再去掉repeat，所以有颜色的就是0.5px的线条
* 缺点：代码量大, 而且需要针对不同边框结构；不能适应圆角样式

```
.box-one-px {	
    background-image: linear-gradient(0deg,black 50%,transparent 50%);
    background-size: 100% 1px;	
    background-repeat: no-repeat;	
}
```

### 使用第三方库解决
* 现在网络上常用的解决设备像素比的第三方库有
	* [amfe-flexible](https://github.com/amfe/lib-flexible)
	* [ant-design-mobile](https://github.com/ant-design/ant-design-mobile/blob/master/components/style/mixins/hairline.less)
	* [vant 组件库](https://github.com/youzan/vant/blob/dev/src/style/mixins/hairline.less)