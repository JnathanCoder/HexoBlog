---
title: 使用 void 0 代替 undefined
date: 2019-05-22 17:02:31
tags:
---

### What...

```js
void 0 === undefined  // true
```

<!-- More -->

### Why？

- 防止`undefined`被重写
  
> 在`ES5之`前，`window`下的`undefined`是可以被重写的，于是导致了某些极端情况下使用`undefined`会出现一定的差错。

所以，用`void 0`是为了防止`undefined`被重写而出现判断不准确的情况。

> 事实上，`void`的返回值都是`undefined`。
> `ES5`之后的标准中，规定了全局变量下的`undefined`值为只读，不可改写的，但是局部变量中依然可以对之进行改写。
> 补充一下：非严格模式下，`undefined`是可以重写的，严格模式则不能重写。

- 节省字节

`void 0`代替`undefined`节省3个字节，类似的还有以下用法：

```js
// 取整操作
parseInt(a,10)  // Before
Math.floor(a)  // Before
a>>0  // Before
~~a  // After
a|0  // After

// 四舍五入操作
Math.round(a) // Before
a+.5|0  // After

// 内置值
undefined  // Before
void 0  // After, 快
0[0]  // After, 略慢

// 内置值
Infinity
1/0

// 布尔值短写法
true;  // Before
false  // Before
!1  // After
!0;  // After

// 获取上下文对象
var global = (function () {
    return this || (0, eval)('this')
}())
// (0, eval)('this') 等价于 eval('this') 正常情况下可以直接使用 eval()
// 部分低版本IE下，不可以直接运行eval()。
```