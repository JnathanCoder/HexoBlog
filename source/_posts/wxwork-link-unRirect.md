---
title: 企业微信中超链接地址跳转
date: 2019-05-21 11:04:11
tags: [企微, 超链接]
---

#### 问题描述：

在`ios(版本号未测试)企业微信(版本号未测试)`中，并且超链接`target`属性是`_blank`时，点击超链接不能正常跳转，当前页面自动刷新一次。
<!-- More -->
而在`ios微信浏览器`以及`android`中未复现。

#### 解决办法

1. 直接修改`target`属性值为`非_blank`即可；
2. 如果是从`富文本编辑器`中出来的`html`内容，可以借助`js`进行处理。示意代码如下：

```js
let oAList = document.querySelectorAll('a')
Array.from(oAList).forEach( (aLink, index) => {
    aLink.getAttribute('target') === '_blank' ? aLink.setAttribute('target', '_self') : null  // 设置成null也可
})
```