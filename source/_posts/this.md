---
title: this + bind + call + apply
date: 2019-05-21 15:05:59
tags: [this, 作用域]
---

### this的指向

> `this`的指向总结起来就是`this永远指向最后调用它的那个对象`

<!-- More -->

例1：

```js
var name = 'global'
function test() {
    var name = 'cherry'
    console.log(this)
    console.log(this.name)
}
test()
// window
// global
```

调用 `test` 方法的地方是 `test()` ，前面没有调用的对象即为 `window` （严格模式下为 `undefined` ），相当于 `window.test()` 。

例2：

```js
var name = 'global'
var obj = {
    name: 'cherry',
    fn: function () {
        console.log(this.name)
    }
}
obj.fn()  // cherry
window.obj.fn()  // cherry
```

`obj.fn()` 取用的是 `obj` 对象 里面的 `name` 值， `window.obj.fn()` 里面，最后调用的是 `obj` 对象，所以也是 `cherry` 。  
补充：假如将 `obj` 对象中 `name` 属性的定义注释掉，那么 `obj.fn()` 将会打印出 `undefined` ，因为只会看最近的调用 `obj` 对象 ，当没有定义时，不会继续向上一个对象 `window` 寻找 `name` 属性。

例3：

```js
var name = 'global'
var obj = {
    name: 'cherry',
    fn: function () {
        console.log(this.name)
    }
}
var temp = obj.fn()
temp()  // global
```

再来看一遍：`this` 永远指向 `最后调用` 它的那个对象。 `obj.fn()` 赋值给 `temp` 时，并没有进行赋值！最后 temp() 调用的时候是 `window.temp()` ，所以打印 `global` 。

例4：

```js
var name = 'global'
function fn () {
    var name = 'cherry'
    innerFn()
    function innerFn () {
        console.log(this.name)
    }
}
fn()  // global
```

例5：

```js
function Func () {
    this.name = 'cherry'
}
var fn = new Func()
console.log(fn.name)  // cherry

// =================================

function Func2 () {
    this.name = 'cherry'
    return {name: 'apple'}  // 返回一个对象 如果name改为name2，fn2.name为undefined
}
function Func3 () {
    this.name = 'cherry'
    return true  // 返回true，非对象（false, 124, 'abc'都可）
}
var fn2 = new Func2()
console.log(fn2.name)  // apple
var fn3 = new Func3()
console.log(fn3.name)  // cherry
```

当我们通过 `new` 关键字构造一个实例对象的过程中，构造函数中的 this 一般情况下指向我们构造出来的实例化对象，因此在构造过程中 `this.name = 'cherry'` 这句代码就相当于给实例对象 `fn` 创建了一个 `name` 属性并赋值 `cherry` 。  
特别的，当构造函数中的 `this` 遇到 `return` 的情况。当构造函数中返回值是一个对象时，`this` 指向的就是返回的那个对象；如果返回值不是对象时，`this` 指向的就是构造函数（这里是 Func3 ）。

### 改变this的指向

例6：

```js
var name = 'global'
var obj = {
    name: 'cherry',
    fn1: function () {
        console.log(this.name)
    },
    fn2: function () {
        setTimeout(function () {
            this.fn1()
        }, 1000)
    }
}
obj.fn2()  // this.fn1 is not a function
```

`setTimeout()` 即为 `window.setTimeout()` ，最后调用的对象是 `window`，而 `window` 中并没有方法 `fn1()` ，故而报错。

**我们通常使用以下几种方法改变this的指向问题：**

- var _this = this

```js
// ...
fn2: function () {
    var _this = this
    setTimeout(function () {
        _this.fn1()  // cherry
    }, 1000)
}
// ...
```

- 箭头函数

```js
// ...
fn2: function () {
    setTimeout( () => {
        this.fn1()  // cherry
    }, 1000)
}
// ...
```

- bind

```js
// ...
fn2: function () {
    setTimeout(function () {
        this.fn1()  // cherry
    }. bind(obj)(), 1000)  // 这里的obj也可以换成this this指向obj
}
// ...
```

- call

```js
// ...
fn2: function () {
    setTimeout(function () {
        this.fn1()  // cherry
    }.call(obj), 1000)  // 这里的obj也可以换成this this指向obj
}
// ...
```

- apply

```js
// ...
fn2: function () {
    setTimeout(function () {
        this.fn1()  // cherry
    }.apply(obj), 1000)  // 这里的obj也可以换成this this指向obj
}
// ...
```

### bind介绍

> `bind()` 方法创建一个 新的函数， 当被调用时，将其 `this` 关键字设置为提供的值。在调用新函数时，在任何提供之前提供一个给定的参数序列。

例7：

```js
var obj = {
    func: function (a, b) {
        console.log('乘积： ' + a * b)
    }
}
obj.func.bind(obj, 2, 8)  // f (a, b) {
                        //     console.log('乘积： ' + a * b)
                        // }
obj.func.bind(obj, 2, 8)()  // 乘积： 16
```

因为 bind 只会重新赋予 this，并不会自动执行，需要手动执行，固有第二个的结果。

### call介绍

> functionObj.call(thisArg[, args1, args2, args3, ...])
>> `thisArg` -> 函数在运行时指定的 `this` 。当为 `null` 或者 `undefined` 时，自动指向 `window` 。当为`string` 、`number` 、`boolean` 类型的时候，`this` 指向各自的包装对象。

```js
function test () {
    console.log('this: ' + this)
}
test.call(null)  // window
test.call(undefined)  // window
test.call('abcd')  // String
test.call(128)  // Number
test.call(false)  // Boolean

// apply() 的结果与 call() 一致
test.apply(null)  // window
// ...
```

例8：

```js
var obj = {
    name: 'hello',
    func: function (a, b) {
        console.log('乘积： ' + a * b)
    }
}
obj.func.call(obj, 2, 5)  // 乘积： 10
```

### apply介绍

> functionObj.apply(thisArg[, argsArr])
>> `apply` 的语法和 `call` 的语法类似，区别在于 `apply` 的参数列表是以 `数组` 或者 `类数组` 形式传入。

例9：

```js
var obj = {
    name: 'hello',
    func: function (a, b) {
        console.log('乘积： ' + a * b)
    }
}
obj.func.apply(obj, [2, 6])  // 乘积： 12
```

### call、apply常见用法

- 将类数组 `ArrayLike` 转换成数组 `Array`

```js
var arraylike = {'1': 'hello', '2': 'world', '3': 'balabala', length: 4}
function ArrTransfer () {
    // return [].slice.call(arguments)
    return Array.prototype.slice.call(arguments)
    // return [].splice.call(arguments, 0)
    // return Array.prototype.splice.call(arguments, 0)
    // return Array.from(arguments)
}
var list = ArrTransfer(arrayLike)  // [{'1': 'hello', '2': 'world', '3': 'balabala', length: 3}]

Array.prototype.slice.call(arrayLike).forEach( (elem, index) => {
    console.log(elem, index)
    // hello 1
    // world 2
    // balabala 3
})
```

- 求最大最小值

```js
var arr = [21, 2, 35, 9, 23, 16, 29]
Math.max.apply(Math, arr)  // 35
Math.min.apply(Math, arr)  // 2
```

### Useful Link

- [全文参考自掘金文章](https://juejin.im/post/59bfe84351882531b730bac2)