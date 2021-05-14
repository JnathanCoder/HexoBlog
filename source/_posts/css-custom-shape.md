---
title: css 常见图形
date: 2020-12-26 21:49:45
tags: [css]
---

### 1. 三角形

#### 1.1 上三角

<div class="up-triangle"></div>

```html
<div class="up-triangle"></div>
```
<!-- more -->
```yaml
.up-triangle {
    display: inline-block;
    width: 0; height: 0;
    border-left: 20px solid transparent;
    border-right: 20px solid transparent;
    border-bottom: 20px solid #00bebe;
}
```


#### 1.2 下三角

<div class="down-triangle"></div>

```yaml
.down-triangle {
    display: inline-block;
    width: 0; height: 0;
    border-left: 20px solid transparent;
    border-right: 20px solid transparent;
    border-top: 20px solid #00bebe;
}
```
```html
<div class="down-triangle"></div>
```

#### 1.3 左三角

<div class="left-triangle"></div>

```yaml
.left-triangle {
    display: inline-block;
    width: 0; height: 0;
    border-top: 20px solid transparent;
    border-bottom: 20px solid transparent;
    border-right: 20px solid #00bebe;
}
```
```html
<div class="left-triangle"></div>
```

#### 1.4 右三角

<div class="right-triangle"></div>

```yaml
.right-triangle {
    display: inline-block;
    width: 0; height: 0;
    border-top: 20px solid transparent;
    border-bottom: 20px solid transparent;
    border-left: 20px solid #00bebe;
}
```
```html
<div class="right-triangle"></div>
```

### 1.5 带线框的三角形

#### 1.5.1 两个三角形叠加 `谷歌翻译使用的方法`

<div class="custom-speaking1"><!--
    --><div>这是对话内容</div><!--
    --><div class="custom-triangle1"></div><!--
--></div>

```yaml
.custom-speaking1 {
    display: inline-block;
    padding: 10px;
    border: 1px solid #00bebe;
    filter: drop-shadow();
    position: relative;
}
.custom-triangle1 {
    position: absolute;
    top: -11px;
    left: 30px;
}
.custom-triangle1:before {
    content: '';
    position: absolute;
    left: -11px;
    border-left: 11px solid transparent;
    border-right: 11px solid transparent;
    border-bottom: 11px solid #00bebe;
}
.custom-triangle1:after {
    content: '';
    position: absolute;
    left: -10px;
    top: 1px;
    border-left: 10px solid transparent;
    border-right: 10px solid transparent;
    border-bottom: 10px solid #fff;
}
```
```html
<div class="custom-speaking1">
    <div>这是对话内容</div>
    <div class="custom-triangle1"></div>
</div>
```

#### 1.5.2 多重渐变背景正方形+旋转 [来源Demo](https://www.zhangxinxu.com/study/201710/css3-linear-gradient-triangle.html)

<div class="custom-speaking2">这是对话内容</div>

```yaml
.custom-speaking2 {
    display: inline-block;
    padding: 10px;
    border: 1px solid #00bebe;
    position: relative;
}
.custom-speaking2:before{
    content: '';
    position: absolute;
    top: 16px; left: -6px;
    width: 10px; height: 10px;
    background: linear-gradient(to top, blue, blue) no-repeat, linear-gradient(to right, green, green) no-repeat, linear-gradient(to right bottom, #fff, #fff 10px, rgba(0, 0, 0, 0) 10px) no-repeat;
    background-size: 10px 1px, 1px 10px, 10px 10px;
    transform: rotate(-45deg);
}
```
```html
<div class="custom-speaking2">这是对话内容</div>
```

> transparent 10px → rgba(0, 0, 0, 0) 10px，新升级Firefox下transparent边缘会有灰色背景渲染bug，使用rgba或hsla白色透明表示可修复。

### 2. 比例可控制的虚线

<div class="dashed-line1"></div> 1:1
<div class="dashed-line2"></div> 3:7

```yaml
.dashed-line1 {
    display: inline-block;
    width: 200px;
    height: 1px;
    background: linear-gradient(to right, black, black 5px, transparent 5px, transparent);
    background-size: 10px 1px;
}
.dashed-line2 {
    display: inline-block;
    width: 200px;
    height: 1px;
    background: linear-gradient(to right, black, black 3px, transparent 3px, transparent);
    background-size: 10px 1px;
}
```
```html
<div class="dashed-line1"></div>
<div class="dashed-line2"></div>
```

### 3. 加号减号

<div class="custom-minus"></div>&emsp;<div class="custom-plus"></div>

```yaml
.custom-minus {
    display: inline-block;
    width: 30px;
    height: 30px;
    border-radius: 3px;
    background: linear-gradient(to right, currentColor, currentColor);
    background-color: #f0f1f2;
    background-position: center;
    background-size: 15px 2px;
    background-repeat: no-repeat;
}
.custom-plus {
    display: inline-block;
    width: 30px;
    height: 30px;
    border-radius: 3px;
    background: linear-gradient(to right, currentColor, currentColor);
    background-color: #f0f1f2;
    background-position: center;
    background-size: 15px 2px;
    background-repeat: no-repeat;
}
```
```html
<div class="custom-minus"></div>
<div class="custom-plus"></div>
```
