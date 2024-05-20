# 自适应布局

> 前端自适应布局是指网页能够根据不同设备的尺寸和屏幕分辨率自动调整布局和样式，以适应不同的屏幕大小，提供更好的用户体验。

> 本文使用ChatGPT辅助编写

## 阅读本文您将收获
* 自适应布局有哪几种
* 如何创建自适应布局

## 自适应布局的种类
* 百分比布局(流式布局)
* 媒体查询布局
* `rem` 响应式布局
* `vw` 和 `vh` 响应式布局
* `flex` 弹性布局

## 创建自适应布局

##### 百分比布局(流式布局)

> 百分比布局(流式布局)是一种常见的自适应布局方式，它通过设置元素的尺寸或位置属性为百分比值来实现页面元素的相对定位和大小调整，以适应不同设备和屏幕尺寸的显示。

```
.container {
    width: 80%; /* 容器宽度占父元素宽度的80% */
    margin: 0 auto; /* 居中显示 */
    border: 1px solid black;
}
  
.box {
    width: 25%; /* 盒子宽度占容器宽度的25% */
    padding-top: 25%; /* 盒子高度为宽度的25%，实现正方形 */
    background-color: skyblue;
    float: left; /* 浮动布局 */
    margin: 1%;
}
```

##### 媒体查询布局

> 媒体查询布局是通过CSS中的媒体查询（`Media Queries`）来实现的。

> 媒体查询允许我们针对不同的设备或者设备特性来应用不同的样式。

> 通过媒体查询，我们可以根据屏幕的宽度、高度、分辨率等特性来动态地调整网页的布局和样式，从而实现响应式设计，使网页在不同设备上都能良好地显示和适应。

```
/* 基础样式 */
.container {
    width: 100%;
    margin: 0 auto;
    padding: 10px;
    box-sizing: border-box;
}
.box {
    width: 100px;
    height: 100px;
    background-color: blue;
    margin: 10px;
    float: left;
}

  /* 媒体查询 */
@media screen and (max-width: 600px) {
    .box {
        width: 50px;
        height: 50px;
    }
}
```

* 媒体查询布局通常用于响应式网页设计种，可以根据设备的特征和用户的行为动态地调整页面布局和样式，提供更好的用户体验。

##### `rem` 响应式布局

> REM（`Root EM`）布局是相对于根元素（html）的字体大小进行计算的布局方式。通过设置根元素的字体大小，我们可以方便地控制整个页面中各个元素的尺寸，从而实现响应式布局。

```
html {
    font-size: 16px; /* 设置根元素字体大小为16px */
}

/* 使用REM单位设置元素的尺寸 */
.box {
    width: 10rem; /* 宽度为10倍的根元素字体大小，即160px */
    height: 5rem; /* 高度为5倍的根元素字体大小，即80px */
    background-color: blue;
}
```

##### `flex` 弹性布局

* `flex` 弹性布局已在其他篇文章中进行详解，[请看这里](https://github.com/programmer-zhang/front-end/tree/master/profiles/HTML_flex.md)