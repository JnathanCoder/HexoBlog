---
title: Diary【四】
date: 2021-04-09 14:05:25
tags:
---

### 对闭包的理解

> 官方解释：闭包是一个拥有许多变量和绑定了这些变量的环境的表达式。
> 社区：闭包是定义在一个函数内部可访问该函数内部局部变量的函数，作用就是让函数外部可以访问函数内部局部变量.

设计闭包主要是为了实现私有变量和方法，可以避免全局污染。不过闭包会常驻内存，会增大内存使用量。
特性：

1. 函数嵌套函数。

2. 函数内部可以引用函数外部的变量和方法。

3. 变量不会被垃圾回收机制回收。


### 判断对象为空

转成json字符串

```js
alert(JSON.stringify(obj) === '{}')
```

for in 循环

```js
const isEmpty = (obj) => {
    for(let item in obj) {
        return false
    } return true
}
```

Object.getOwnPropertyNames()

```js
alert(Object.getOwnPropertyNames(obj).length === 0)
```

Object.keys()

```js
alert(Object.keys(obj).length === 0)
```