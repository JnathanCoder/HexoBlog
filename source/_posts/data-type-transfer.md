---
title: 数据类型转换
date: 2020-04-19
tags: [数据类型, 类型转换]
---

基本数据类型有 `Number`、 `String`、 `Boolean`、 `Null`、 `Undefined`、`Object`，ES6新增了`Symbol` 类型。

### 1. 原始值转布尔

在Javascript中。只有6种值可以被转换成false，其余均为true：
`false`、`undefined`、`null`、`NaN`、`0`、`''`

```js
    console.log(Boolean(false))  // false
    console.log(Boolean(undefined))  // false
    console.log(Boolean(null))  // false
    console.log(Boolean(NaN))  // false
    console.log(Boolean(0))  // false
    console.log(Boolean(''))  // false
    // Boolean不传任何参数时 返回false
    console.log(Boolean())  // false
```

<!-- More -->

### 2. 原始值转数字

使用`Number`函数将数据转换成数字类型，参数无法转换成数字的，返回NaN。
根据规范，如果Number函数不传参数，返回0，如果有参数，调用`toNumber(value)`,其对应结果如下：

|参数类型|结果|
|:---|:---|
|undefined|NaN|
|null|0|
|布尔值|true: 1, false: 0|
|Number|对应的值|
|String|比较复杂，看具体例子|

```js
    console.log(Number())  // 0

    console.log(Number(undefined))  // NaN
    console.log(Number(null))  // 0
    console.log(Number(true))  // 1
    console.log(Number(false))  // 0
    console.log(Number(88))  // 88

    console.log(Number("123"))  // 123
    console.log(Number("-123"))  // -123
    console.log(Number("1.2"))  // 1.2
    console.log(Number("000123"))  // 123
    console.log(Number("-000123"))  // -123
    console.log(Number("0x11"))  // 17
    console.log(Number(""))  // 0
    console.log(Number(" "))  // 0
    console.log(Number("123 123"))  // NaN
    console.log(Number("foo"))  // NaN
    console.log(Number("100a"))  // NaN
```

如果通过 `Number` 转换函数传入一个字符串，它会试图将其转换成一个整数或浮点数，而且会忽略所有前导的 `0`，如果有一个字符不是数字，结果都会返回 `NaN`。如果字符串前缀是`0x`或者`0X`，将转换为`十六进制`数。

### 3. 原始值转字符

使用 `String` 函数将类型转换成字符串类型。
根据规范，如果不传递参数，返回空字符串，如果有参数，调用`toString(value)`，其对应结果如下：


|参数类型|结果|
|:---|:---|
|undefined|"undefined"|
|null|"null"|
|Boolean|true: "true", false: "false"|
|Number|比较复杂，看具体例子|
|String|对应的值|


```js
    console.log(String()) // 空字符串

    console.log(String(undefined)) // undefined
    console.log(String(null)) // null
    console.log(String(false)) // false
    console.log(String(true)) // true
    console.log(String('678')) // 678

    console.log(String(0)) // 0
    console.log(String(-0)) // 0
    console.log(String(NaN)) // NaN
    console.log(String(Infinity)) // Infinity
    console.log(String(-Infinity)) // -Infinity
    console.log(String(1)) // 1
```

注意这里的 ToString 和上一节的 ToNumber 都是底层规范实现的方法，并没有直接暴露出来。

### 4. 对象转字符串和数字

几种基本数据类型中，除了Null和Undefined类型外，其余5种原型链上都有valueOf()和toString()函数。这两个函数主要在隐式类型转换时会被自动调用。

#### 4.1 同时重写对象的toString方法和valueOf方法

```js
var aaa = {
    i: 10,
    valueOf: function() { 
        console.log('调用valueOf...')
        return this.i + 30
    },
    toString: function() { 
        console.log('调用toString...')
        return this.i + 10
    }
}
console.log(aaa > 20); // 调用valueOf...  true
console.log('' + aaa);  //  调用valueOf...  40
console.log(++aaa); // 调用valueOf...  41
// 在不执行上一步 console.log(++aaa) 的前提
console.log(+aaa); //  调用valueOf...  40
// 在执行上一步 console.log(++aaa) 的前提 此时aaa变成了字符串41
console.log(+aaa); // 41 调用原型链上默认的valueOf()
console.log(aaa == 40) // 调用valueOf... true

// 上面的步骤没执行的前提下 否则aaa变成了字符串41 此时输出41
console.log(aaa); // {i: 10, valueOf: ƒ, toString: f}
console.log(String(aaa)) // 调用toString... 20
```

总结：涉及到操作符的问题，valueOf的优先级更高；涉及到显示问题，toString方法优先级更高。

#### 4.2 只重写toString方法

```js
var bbb = {
    i: 10,
    toString: function() {
        console.log('调用toString...')
        return this.i + 10
    }
}
console.log(bbb > 20); // 调用toString... false
console.log('' + bbb);  //  调用toString... 20
console.log(++bbb); // 调用toString... 21
// 在不执行上一步 console.log(++bbb) 的前提
console.log(+bbb); // 调用toString... 20
// 在执行上一步 console.log(++bbb) 的前提 此时bbb变成了字符串21
console.log(+bbb); // 21 调用原型链上默认的valueOf()
console.log(bbb == 20) // 调用toString... true

// 
console.log(bbb); // {i: 10, toString: ƒ} 没调用重写的toString()方法，调用了原型链上默认的valueOf()
console.log(String(bbb)) // 调用toString... 20
```

总结： toString方法比原型链上的valueOf方法优先级高