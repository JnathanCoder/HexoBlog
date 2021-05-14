---
title: prepare
date: 2021-04-22 17:32:33
tags: 面试
---

### 1. 盒子模型

默认为 标准盒模型 - content-box
W3C盒子模型（又叫标准盒模型），元素的宽高大小表现为 `内容content` 的大小 content-box
IE盒子模型（又叫怪异盒子模型），元素的宽高大小表现为 `内容content` + `内边距padding` + `边框border` 的大小。背景颜色会延伸到边框的外沿。


### 2. BFC(块级格式化上下文 Block Formatting Context)

BFC是一个独立的布局环境，BFC内部的元素布局与外部互不影响.

**布局规则**
内部的box会在垂直方向一个一个放置；(即使不在BFC内，块级盒子也是这样排列的😆)
属于同一个BFC内的两个box垂直方向上的margin会发生重叠；
BFC的区域不会与float box重叠；
BFC就是页面上的一个隔离的独立容器，容器里面的子元素不会影响到外面的元素，反之亦然；
计算BFC的高度时，浮动子元素也参与计算。

**可解决的问题**

<!-- More -->

2.1 浮动元素令父元素高度塌陷

```html
.parent {
    background: olive;
}
.child {
    width: 200px;
    height: 200px;
    background: oldlace;
    float: left;
}

<div class="parent">
    <div class="child">child</div>
</div>
```
效果：<img src="/assets/img/prepare/demo3.jpg" style="width: 300px;">

方法：给父元素开启BFC, 比如加上overflow: hidden
原理：计算BFC的高度时，浮动子元素也参与计算

```html
.parent {
    background: olive;
    overflow: hidden;
}
.child {
    width: 200px;
    height: 200px;
    background: oldlace;
    float: left;
}
```
效果：<img src="/assets/img/prepare/demo4.jpg" style="width: 300px;">

2.2 非浮动元素被浮动元素遮盖

```html
.box1 {
    width: 200px;
    height: 200px;
    background: oldlace;
    float: left;
}
.box2 {
    width: 300px;
    height: 300px;
    background: olive;
}

<div class="box1">box1</div>
<div class="box2">box2</div>
```
效果：<img src="/assets/img/prepare/demo1.jpg" style="width: 300px;">

方法：将为浮动元素开启BFC, 比如加上overflow: hidden
原理：BFC的区域不会与float box重叠

```html
.box1 {
    width: 200px;
    height: 200px;
    background: oldlace;
    float: left;
}
.box2 {
    width: 300px;
    height: 300px;
    background: olive;
    overflow: hidden;  // This line
}
```
效果：<img src="/assets/img/prepare/demo2.jpg" style="width: 300px;">

浮动的盒子会遮盖下面的盒子，但是下面盒子里的文字是不会被遮盖的，文字反而还会环绕浮动的盒子。将本例中box2里文字加多点，会有如下效果：
<img src="/assets/img/prepare/demo9.jpg" style="width: 300px;">
要去除这种环绕效果，只需开启box2的BFC（oveflow: hidden）即可。

2.3 垂直方向上外边距重合的问题

```html
<style>
    .box1 {
        margin-bottom: 10px;
        width: 200px;
        height: 200px;
        background: oldlace;
    }
    .box2 {
        margin-top: 10px;
        width: 300px;
        height: 300px;
        background: olive;
    }
    .box-outer {
        overflow: hidden;
    }
</style>

<div class="box1">box1</div>
<div class="box2">box2</div>
```
效果：<img src="/assets/img/prepare/demo5.jpg" style="width: 200px;">
实际上呈现的效果只有10px的间距。
在常规文档流中，两个兄弟盒子之间的垂直距离是由他们的外边距所决定的，但不是他们的两个外边距之和，而是以较大的为准。

方法：创建一个BFC，避免相邻两个div之间的margin重叠
原理：BFC就是页面上的一个隔离的独立容器，容器里面的子元素不会影响到外面的元素，反之亦然

```html
<div class="box1">box1</div>
<div class="box-outer">
    <div class="box2">box2</div>
</div>
```
效果：<img src="/assets/img/prepare/demo6.jpg" style="width: 200px;">
实际上呈现的效果变成了20px。

2.4 实现两栏自适应布局（也可实现三栏布局）

```html
<style>
    .box1 {
        width: 200px;
        height: 200px;
        background: oldlace;
    }
    .box2 {
        height: 300px;
        background: olive;
    }
</style>

<div class="box1">box1</div>
<div class="box2">box2</div>
```
效果：<img src="/assets/img/prepare/demo7.jpg" style="width: 300px;">

方法：左div设置左浮动，右div开启BFC
原理：BFC的区域不会与float box重叠

```html
<style>
    .box1 {
        width: 200px;
        height: 200px;
        background: oldlace;
    }
    .box2 {
        height: 300px;
        background: olive;
    }
</style>
```
效果：<img src="/assets/img/prepare/demo8.jpg" style="width: 300px;">


### 3. nth-child 和 nth-of-type

p:nth-child(n) 是选中父元素下的第N个元素，如果它不是p元素，则没有任何效果
p:nth-of-type(n) 是选中父元素下的第N个p`类型`元素

```html
<style>
    .parent p:nth-child(2) {
        /* 第二个不是p元素 没有任何效果 */
        color: red;
    }
    .parent p:nth-child(4) {
        /* 第四个是p元素 文字4呈现红色字体 */
        color: red;  // 第四个是p元素 文字4呈现红色字体
    }
    .parent p:nth-of-type(2) {
        /* parent下第二个p元素是text-4 呈现红色字体 */
        color: red;
    }

    /* 不指定标签类型时 */
    .parent :nth-child(2) {
        /* parent下第二个元素是text-4 呈现红色字体 */
        color: red;
    }
    .parent :nth-of-type(2) {
        /* parent下第二个p元素是text-4 呈现红色字体；第二个h5元素是text-3 也呈现红色字体 */
        color: red;
    }
</style>

<div class="parent">
    <p>text-1</p>
    <h5>text-2</h5>
    <h5>text-3</h5>
    <p>text-4</p>
    <p>text-5</p>
    <h5>text-6</h5>
</div>
```
