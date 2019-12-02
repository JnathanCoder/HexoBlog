---
title: 日志内容【二】
date: 2019-05-26 14:08:31
toc: true
tags:
---

### 1. substr() 和 substring()

- substr

> stringObject.substr(startIndex[, length])

<!-- More -->

```js
var str = ‘abcdefg'

str.substr(2)  // ‘cdefg’
str.substr(-1) => str.substr(6)  // ‘fg’
str.substr(6)  // ‘'
str.substr(2,2)  // ‘cd’
str.substr(2,7)  // ‘cdefg’
str.substr(2,-1) // ''
```

- substring

> stringObject.substring(startIndex[, stop]) 注意stop的含义

```js
var str = ‘abcdefg'

str.substring(2)  // ‘cdefg’
str.substring(2,4)  // ‘cd’
str.substring(2,9)  // ‘cdefg’
str.substring(-2) => str.substring(0)  // ‘abcdefg’
str.substring(2,-4) => str.substring(2,0) => str.substring(0,2)  // ‘ab'
```

### 2. slice() 和 splice()

- slice

> stringObject.slice(startIndex[, stop])
> arrayObject.slice(startIndex[, stop])
> stop  缺省时 默认一直到最后

```js
var str = ‘abcdefg’

str.slice(2)  // ‘cdefg'
str.slice(0)  // ‘abcdefg’
str.slice(-2) => str.slice(5)  // ‘fg’
str.slice(8)  // ‘’ 空串 
str.slice(2,4)  // ‘cd’
str.slice(2,6)  // ‘cdef’
str.slice(2,18)  // ‘cdefg’
str.slice(4,2)  // ‘’ 空串
str.slice(4,-1) => str.slice(4,6)  // ‘ef’

// 数组方法类似
```

- splice

> arrayObject.splice(startIndex, length[, item1, item2…])
> splice操作会改变原数组  返回被移除的元素数组

```js
var arr = [‘a’,’b’,’c’,’d’,’e’,’f’,’g’]

arr.splice(2,0)  // 空数组 arr = [‘a’,’b’,’c’,’d’,’e’,’f’,’g’]
arr.splice(2,8)  // arr = [‘a’, ‘b’, ‘c']
arr.splice(2,-5)  // 返回arr  arr没有变化

arr.splice(8,2)  // 返回arr  arr没有变化
arr.splice(2,2,'11',’22')  // 返回[‘a’, ‘b’, ’11’, ’22’, ‘e’, ‘f’, ‘g’]  arr = [‘c’, ‘d']
```

### 3. 检测数据类型

- typeof

> typeof  是解释器内部实现，根据 ECMA-262 规定的几种类型的值来返回类型名称。
> 但是  typeof  的应用场景非常有限，基本上只能判断出来使用字面量方式赋值的基本数据类型。

```js
typeof 23  // number
typeof '124'  // string
typeof true  // boolean
typeof undefined  // undefined
typeof {}  // object
typeof null  // object
typeof [1, 3, 8]  // object
typeof function fn () {}  // function
typeof (new Number(33))  // object
typeof (new String('hello'))  // object
```

从上面例子可以看出， typeof  检测出来的类型不够准确，具有相当大的局限性。

- instanceof

> instance  检测类型的原理大致为: 判断左边的对象的隐式原型  __proto__  是否和右边对象的显式原型  prototype  相等。 很像  new  操作，这样就可以判断指定的某个对象是否为某个构造函数的实例。  instance  的局限性应该是不能检测基本数据类型。另外一点，不同  window  或者  iframe  之间对象类型检测不能使用  instanceof  来检测。

```js
'123' instance String  // false

Number instanceof Number  // true

new String('hello') instance String  // true

new Number(33) instance Number  // true
```

- Object.prototype.toString

```js
Object.prototype.toString.call(33)  // [object Number]

Object.prototype.toString.call('hello')  // [object String]

Object.prototype.toString.call([])  // [object Array]

Object.prototype.toString.call({})  // [object Object]

Object.prototype.toString.call(true)  // [object Boolean]

Object.prototype.toString.call(null)  // [object null]

Object.prototype.toString.call(undefined)  // [object undefined]

Object.prototype.toString.call(function fn () {})  // [object Function]

Object.prototype.toString.call(new Number(33))   // [object Number] 
```

- constructor

> constructor  属性返回对创建此对象的数组函数的引用。
> constructor  不能检测出null和undefined两种，会报错。

```js
(33).constructor == Number  // true

('hello'.constructor == String  // true

([]).constructor == Array  // true

({}).constructor == Object  // true

(true).constructor == Boolean  // true

(null).constructor == null  // TypeError: Cannot read property 'constructor' of null

(undefined).constructor == undefined  // TypeError: Cannot read property 'constructor' of undefined

var fn = function () {}
fn.constructor == Function  // true

var number = new Number(66)
number.constructor == Number  // true
number.constructor == Object  // false
```

- 我是分割线

- Array.isArray()

### 4. ES5 Array Function

- forEach

> Array.forEach(callbackFn[, thisObj])
> callbackFn (value, index, arr)
> `thisObj` 默认为 `window`

```js
[1, 2, 5, 8].forEach( function (ele, index, arr) {
    console.log(ele, index, arr)
})
```

- map

> Array.map(callbackFn[, thisObj])
> callbackFn (value, index, arr)

```js
[1, 2, 5, 8].map(function (ele, index, arr) {
    return ele * index  // [0, 2, 10, 24]
})
```

- filter

> Array.filter(callbackFn[, thisObj])
> callbackFn (value, index, arr), 需要返回 Boolean 值

```js
[0, 2, 5, 8].filter(function (ele, index, arr) {
    return ele  // [2, 5, 8]
})

[0, 2, 5, 1].filter(function (ele, index, arr) {
    return ele <= index  // [0, 1]
})
```

- some

> Array.some(callbackFn[, thisObj])
> callbackFn (value, index, arr)

```js
[1, 2, 5, 8].some(function (ele, index, arr) {
    return ele > 5  // true (8 > 5)
})
```

- every

> Array.every(callbackFn[, thisObj])
> callbackFn (value, index, arr)

```js
[1, 2, 5, 8].every(function (ele, index, arr) {
    return ele > 5  // false (1, 2, 5 均不大于5)
})
```

- indexOf

> Array.indexof(searchEle[, fromIndex])
> 第一个出现的 `searchEle` 的索引位置
> `searchEle` 要查找的元素
> `fromIndex` 开始查找的索引位置，缺省或不符合规范时为 0

```js
[1, 2, 5, 8].indexOf(2)  // 1
[1, 2, 5, 8].indexOf(2, '0')  // 1
[1, 2, 5, 8].indexOf(2, 'f') => [1, 2, 5, 8].indexOf(2, 0)  // 1
[1, 2, 5, 8].indexOf(2, 2)  // -1
```

- lastIndexOf

> Array.lastIndexOf(searchEle[, fromIndex])
> 与 `indexOf` 不同在于此方法是从后面元素开始查找的
> `searchEle` 要查找的元素
> `fromIndex` 开始查找的索引位置，缺省时或大于等于 Array.length 为 Array.length - 1

```js
[1, 2, 5, 8, 5].lastIndexOf(5)  // 4
[1, 2, 5, 8, 5].lastIndexOf(5, 4)  // 4
[1, 2, 5, 8, 5].lastIndexOf(5, 8) => [1, 2, 5, 8, 5].lastIndexOf(5, 4)  // 4
[1, 2, 5, 8, 5].lastIndexOf(5, 'f')  // -1
[1, 2, 5, 8, 5].lastIndexOf(5, 3)  // 2
[1, 2, 5, 8, 5].lastIndexOf(5, 1)  // -1
```

- reduce

> Array.reduce(callbackFn[, initValue])
> callbackFn(previous, current, index, arr)
> `previous` 前一次迭代计算出来的值
> `current` 当前迭代的值
> `initValue` 初始值, 未指定时取数组的第一个元素

```js
[1, 2, 5, 8].reduce(function (preV, curV, index, arr) {
    return preV + curV
}, 10)  // 26
```

```js
// 梳理一下计算过程：
// 初始化的时候
preV = initValue = 10, curV = 1, index = 0

// 第一次计算
preV = 10 + 1 = 11, curV = 2, index = 1

// 第二次计算
preV = 11 + 2 = 13, curV = 5, index = 2

// 第三次计算
preV = 13 + 5 = 18, curV = 8, index = 3

// 第四次计算
preV = 18 + 8 = 26, curV = undefined (退出)
```

- reduceRight

> 和 `reduceRight` 用法基本一致，不过是从数组后面开始迭代的
> Array.reduceRight(callbackFn[, initValue])
> callbackFn(previous, current, index, arr)
> `previous` 前一次迭代计算出来的值
> `current` 当前迭代的值

```js
[1, 2, 5, 8].reduceRight(function (preV, curV, index, arr) {
    if (index == 0) {
        return preV - curV
    }
    return preV + curV
}, 10)  // 24
```

```js
// 梳理一下计算过程：
// 初始化的时候
preV = initValue = 10, curV = 8, index = 3

// 第一次计算
preV = 10 + 8 = 18, curV = 5, index = 2

// 第二次计算
preV = 18 + 5 = 23, curV = 2, index = 1

// 第三次计算
preV = 23 + 2 = 25, curV = 1, index = 0

// 第四次计算
preV = 25 - 1 = 24, curV = undefined (退出)
```

### [参考文章](https://juejin.im/post/59b5540c5188257e8769e95d)