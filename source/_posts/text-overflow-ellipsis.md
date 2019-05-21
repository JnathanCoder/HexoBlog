---
title: CSS文本超出容器宽度使用省略号代替（不考虑js方法）
date: 2019-04-01
tags: [ellipsis, 换行, 省略号]
---

#### 方法一 ：常规方法（适用于`webkit`内核浏览器）

```html
<div class="container text-ellipsis">单行文字超出省略号单行文字超出省略号单行文字超出省略号单行文字超出省略号单行文字超出省略号单行文字超出省略号单行文字超出省略号单行文字超出省略号</div>
<div class="container text-ellipsis-multi">多行文字超出省略号多行文字超出省略号多行文字超出省略号多行文字超出省略号多行文字超出省略号多行文字超出省略号多行文字超出省略号多行文字超出省略号多行文字超出省略号多行文字超出省略号多行文字超出省略号</div>
```

<!-- More -->

```html
.container {
    display: inline-block;
    width: 333px;
    height: 60px;
    font-size: 16px;
    border: 1px solid #00bebe;
}
.text-ellipsis {
    overflow: hidden;
    text-overflow: ellipsis;
    white-space: nowrap;
}
.text-ellipsis-multi {
    line-height: 20px;
    overflow: hidden;
    text-overflow: ellipsis;
    display: -webkit-box;
    -webkit-line-clamp: 3;  // 控制显示的行数
    -webkit-box-orient: vertical
}
```

在`webkit`浏览器中的表现：![单行省略](/assets/img/webkit-single.jpg) ![多行省略](/assets/img/webkit-multi.jpg)

### 方法二：利用伪类+`float`（适用于大部分浏览器）

```html
<div class="wrap">
    <div class="text">多行文字超出省略号多行文字超出省略号多行文字超出省略号多行文字超出省略号多行文字超出省略号多行文字超出省略号多行文字超出省略号多行文字超出省略号多行文字超出省略号多行文字超出省略号多行文字超出省略号</div>
</div>
```

```html
.wrap {
    width: 333px;
    height: 60px;
    line-height: 20px;
    overflow: hidden;
    border: 1px solid #00bebe;
}
.wrap:before {
    float: left;
    content: '';
    width: 5px;
    height: 100%;
}
.wrap:after {
    float: right;
    content: '...';
    width: 20px;
    margin-left: -20px;
    height: 20px;
    line-height: 20px;
    margin-right: 5px;
    background: #fff;
    position: relative;
    top: -20px;
    left: 100%;
}
.wrap .text {
    float: right;
    width: 100%;
    margin-left: -5px;
    word-break: break-all;
}
```

在`非webkit`内核浏览器中的表现：![非webkit浏览器多行省略](/assets/img/unwebkit-multi.jpg)

可以看到效果还是不那么尽人意，可以在此基础上再根据实际情况修改相应代码，比如`...`用渐变色代替现在的白色，会显得平滑。另外，也可以搭配js进一步处理，可以参考[这里](https://blog.csdn.net/lhban108/article/details/82757564)。