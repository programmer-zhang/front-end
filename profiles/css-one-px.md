## 阅读本文您将收获到
* 为什么会造成一像素的线视觉上会变粗
* 如何画一像素的线

## 明确几个概念
### 物理像素(设备像素)
* 显示屏幕的最小物理单位
* 每个物理像素包含自己的颜色、高宽等，不可再细分。
* 物理像素是在设备出厂是设定的，设备一旦造出来就不会变大小和数量。官方在产品说明书上写的1920x1080就是说的物理像素。

### 设备独立像素
* 设备独立像素（也叫密度无关像素），可以认为是计算机坐标系统中得一个点，这个点代表一个可以由程序使用的虚拟像素（比如：css像素），有时我们也说成是逻辑像素，由相关系统转换为物理像素。
* 所以说，物理像素和设备独立像素之间存在着一定的对应关系，这就是接下来要说的设备像素比。

### 设备像素比
* 设备像素比（简称dpr）定义了物理像素和设备独立像素的对应关系。
* 它的值可以按如下的公式的得到：

```
// 在某一方向上，x方向或者y方向，下图dpr=2
// 设备像素比(dpr)=物理像素/逻辑像素(px) 
```

## 视觉误差原因
* 因为屏幕的分辨率和浏览器的的分辨率存在换算关系，所以1像素的线在屏幕上会占用2个或者2个以上视觉像素，这点在移动端尤其明显。

## 实现方法
### 使用伪类解决
* 使用 `tansform: scale(.5);` 将高度缩小一半

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

```
//1px像素线条	
&lt;meta name="viewport" content="width=device-width,initial-scale=1.0,user-scalable=0"&gt;	
//0.5像素线条	
&lt;meta name="viewport" content="width=device-width,initial-scale=0.5,user-scalable=0"&gt;
```

### 使用 box-shadow 模拟边框

```
.box-one-px {	
  box-shadow: inset 0px -1px 1px -1px #c8c7cc;	
}
```

### 使用 border-image
* 需要自己制作一个0.5像素的线条作为背景图片

```
.box-one-px {	
    border-width: 0 0 1px 0;	
    border-image: imageUrl 2 0 round;	
}
```

### 使用 背景渐变 linear-gradient
* 利用linear-gradient利用背景图片渐变，从有色到透明，默认方向从上到下，从0deg到50%的地方颜色是边框颜色，然后下边一半颜色就是透明了。然后设置背景宽度100%，高度是1px，再去掉repeat，所以有颜色的就是0.5px的边框

```
.bg_border {	
    background-image: linear-gradient(0deg,black 50%,transparent 50%);
    background-size: 100% 1px;	
    background-repeat: no-repeat;	
}
```