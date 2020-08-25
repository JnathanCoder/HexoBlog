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
    -webkit-box-orient: vertical;
}
```

在`webkit`浏览器中的表现：![单行省略](/assets/img/webkit-single.jpg) ![多行省略](/assets/img/webkit-multi.jpg)

### 方法二：利用伪类+`float`（适用于大部分浏览器）

原理讲解参考[这里](https://juejin.im/post/5dc15b35f265da4d432a3d10)

```html
<div class="wrap">
    <div class="text">多行文字超出省略号多行文字超出省略号多行文字超出省略号多行文字超出省略号多行文字超出省略号多行文字超出省略号多行文字超出省略号多行文字超出省略号多行文字超出省略号多行文字超出省略号多行文字超出省略号</div>
</div>
```

```html
.wrap {
    background: #099;
    max-height: 40px;
    line-height: 20px;
    overflow: hidden;
}
.wrap:before {
    float: left;
    content: '';
    width: 20px;
    height: 40px;
}

.wrap .text {
    float: right;
    width: 100%;
    margin-left: -20px;
    word-break: break-all;
}
.wrap:after {
    float: right;
    content: '...';
    width: 20px;
    height: 20px;
    <!-- 下方三行代码 调整省略号位置 -->
    <!-- 未超出时，省略号在wrap外面，被隐藏 -->
    position: relative;
    left: 100%;
    transform: translate(-100%, -100%);
}
```

在`非webkit`内核浏览器中的表现：![非webkit浏览器多行省略](/assets/img/unwebkit-multi.jpg)

可以看到效果还是不那么尽人意，可以在此基础上再根据实际情况修改相应代码，比如`...`用渐变色代替现在的白色，会显得平滑。另外，也可以搭配js进一步处理，可以参考[这里](https://blog.csdn.net/lhban108/article/details/82757564)。 也可使用封装好的 [vue组件](https://github.com/xm2by/multiline-ellipsis) 。

更详细的文章参考[这里](https://juejin.im/post/5dc15b35f265da4d432a3d10)。