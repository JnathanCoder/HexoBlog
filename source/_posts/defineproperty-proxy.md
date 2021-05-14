---
title: defineproperty-proxy
date: 2020-08-31 16:25:17
toc: true
tags: [defineProperty, proxy]
---

在一篇 [掘金文章](https://juejin.im/post/6861725116389130254) 中看到这样一道题目，大概是以下的需求。

补全Observe函数，使得执行 `proxyObj.name = '大大'` 时，打印 `属性[name]的值被修改为[大大]`。

```js
function Observe(obj, callback) {
    // TO DO
}

let oldObj = {
    name: '小小',
    uid: 108
}
const proxyObj = Observe(oldObj, (key, value) => {
    console.log(`属性[${key}]的值被修改为[${value}]`)
})
```

<!-- more -->

### 1. Object.defineProperty

```js
function Observe(obj, callback) {
    const observedObj = {}
    Object.keys(obj).forEach( (key) => {
        Object.defineProperty(observedObj, key, {
            configurable: true,
            enumerable: true,
            get: () => {
                console.log('emit getter', key)
                return obj[key]
            },
            set: (newVal) => {
                console.log('emit setter', key)
                obj[key] = newVal
                callback(key, newVal)
            }
        })
    })

    return observedObj
}

let oldObj = {
    name: '小小',
    uid: 108
}
const proxyObj = Observe(oldObj, (key, value) => {
    console.log(`属性[${key}]的值被修改为[${value}]`)
})

// output:
// emit getter name
// 小小
console.log(proxyObj.name)

// output:
// emit setter name
// 属性[name]的值被修改为[大大]
proxyObj.name = '大大'
```

### 2. Proxy

```js
function Observe(obj, callback) {
    return new Proxy(obj, {
        get: (target, key) => {
            console.log('emit getter', key)
            return target[key]
        },
        set: (target, key, newVal) => {
            console.log('emit setter', key)
            obj[key] = newVal
            callback(key, newVal)
        }
    })
}

let oldObj = {
    name: '小小',
    uid: 108
}
const proxyObj = Observe(oldObj, (key, value) => {
    console.log(`属性[${key}]的值被修改为[${value}]`)
})

// output:
// emit getter name
// 小小
console.log(proxyObj.name)

// output:
// emit setter name
// 属性[name]的值被修改为[大大]
proxyObj.name = '大大'
```

### 3. 对比

> 在Vue2.0中，数据双向绑定就是通过Object.defineProperty去监听对象的每一个属性，然后在get,set方法中通过发布订阅者模式来实现的数据响应，但是存在一定的缺陷，比如只能监听已存在的属性，对于新增删除属性就无能为力了，同时无法监听数组的变化，所以在Vue3.0中将其换成了功能更强大的Proxy。

在上面的代码中，如果执行 `proxyObj.newVariable = 'haha'`，defineProperty实现将不能触发setter方法，Proxy实现能够触发。

#### 4. 了解Proxy

> Proxy 对象用于定义基本操作的自定义行为（如属性查找、赋值、枚举、函数调用等）

#### 4.0 基本语法

```js
const proxy = new Proxy(target, handler)
```

在上面的代码中，已经使用到了handler里的get与set。除此之外还有11个方法，共计13个。重点描述几个常用的方法。

#### 4.1 `handler.get(target, key, receiver)`

- 属性读取操作的捕捉器。当通过proxy去读取对象里面的属性的时候，会进入到get钩子函数里面。
- `target` 目标对象
- `key` 被获取的属性名
- `receiver` Proxy或者集成Proxy的对象

```js
const proxy = new Proxy({}, {
    get(target, key, receiver) {
        console.log('emit getter', target, key, receiver)
        return target[key]
    }
})
```

如果违背了以下的约束，proxy会抛出 TypeError

- 如果要访问的目标属性是 `不可写` 并且 `不可配置` 的，则返回的值必须与该目标属性的值相同。
- 如果要访问的目标属性没有配置访问方法，即get方法是undefined的，则返回值必须为undefined。(没理解，未配置get方法，返回值怎么定义?)

```js
var obj = {}
Object.defineProperty(obj, 'age', {
    configurable: false,
    writable: false,
    value: 10
})
var p = new Proxy(obj, {
    get(target, key) {
        return 20
    }
})
// Uncaught TypeError: 'get' on proxy: property 'age' is a read-only and non-configurable data property on the proxy target but the proxy did not return its actual value (expected '10' but got '20')
console.log(p.age)
```

#### 4.2 `handler.set(target, key, value, receiver)`

- 属性设置操作的捕捉器。当通过proxy去为对象设置修改属性的时候，会进入到set钩子函数里面。
- `target` 目标对象
- `key` 将被设置的属性名或 Symbol
- `value` 新属性值
- `receiver` 最初被调用的对象。通常是 proxy 本身，但 handler 的 set 方法也有可能在原型链上，或以其他方式被间接地调用（因此不一定是 proxy 本身）。比如：假设有一段代码执行 obj.name = "jen"， obj 不是一个 proxy，且自身不含 name 属性，但是它的原型链上有一个 proxy，那么，那个 proxy 的 set() 处理器会被调用，而此时，obj 会作为 receiver 参数传进来。
- `返回值` set() 方法应当返回一个布尔值
  - 返回 true 代表属性设置成功。
  - 在严格模式下，如果 set() 方法返回 false，那么会抛出一个 TypeError 异常。

如果违背了以下的约束，proxy会抛出 TypeError

- 若目标属性是一个不可写以及不可配置的数据属性，则不能改变它的值。(但是经过测试，并没有抛出错误❌)
- 如果目标属性没有配置存储方法，即 [[Set]] 属性的是 undefined，则不能设置它的值。(同样的疑问，没有set方法，如何设置值呢?)
- 在严格模式下，如果 set() 方法返回 false，那么也会抛出一个 TypeError 异常。

```js
var p = new Proxy({}, {
    set(target, key, value, receiver) {
        target[key] = value
        // 非严格模式下可省略
        return true
    }
})
p.age = 10
```

```js
"use strict"
var p = new Proxy({}, {
    set(target, key, value, receiver) {
        target[key] = value
        // 严格模式没有返回值或返回false 会抛出TypeError
        return false
    }
})
// Uncaught TypeError: 'set' on proxy: trap returned falsish for property 'age'
p.age = 10
```

```js
var obj = {}
Object.defineProperty(obj, 'age', {
    configurable: false,
    writable: false,
    value: 10
})
var p = new Proxy(obj, {
    set(target, key, value, receiver) {
        target[key] = value
        return true
    }
})
// 设置成功 没有抛错误❌
p.age = 20
```

对比Vue2.x

> 在使用Vue2.0的时候，如果给对象添加新属性的时候，往往需要调用 `$set`, 这是因为Object.defineProperty只能监听已存在的属性，而新增的属性无法监听，而通过 `$set` 相当于手动给对象新增了属性，然后再触发数据响应。但是对于Vue3.0来说，因为使用了Proxy，在他的set钩子函数中是可以监听到新增属性的，所以就不再需要使用

#### 4.3 `handler.has(target, key)`

- in 操作符的捕捉器。当使用in判断属性是否在proxy代理对象里面时，会触发。
- `target` 目标对象
- `key` 需要检查是否存在的属性.
- `返回值` has 方法返回一个 boolean 属性的值.

```js
const obj = {
  publicFn() {},
  _privateFn() {}
}
const p = new Proxy(obj, {
    has(target, key) {
        if (key.startWith('_')) {
            return false
        }
        return target[key]
    }
})
console.log('publicFn' in obj)  // true
console.log('_privateFn' in obj)  // false
```

如果违反了下面这些规则,  proxy 将会抛出 TypeError:

- 如果目标对象的某一属性本身不可被配置，则该属性不能够被代理隐藏(怎么写示例...)
- 如果目标对象为不可扩展对象，则该对象的属性不能够被代理隐藏

```js
var obj = { age: 10 }
Object.preventExtensions(obj)
var p = new Proxy(obj, {
    has: function(target, key) {
        return false
    }
})
// Uncaught TypeError: 'has' on proxy: trap returned falsish for property 'a' but the proxy target is not extensible
console.log('age' in p)
```

#### 4.4 `handler.deleteProperty(target, key)`

- `delete` 操作符的捕捉器。当使用delete去删除对象里面的属性的时候，会进入deleteProperty`钩子函数
- `target` 目标对象
- `key` 待删除的属性名
- `返回值` deleteProperty 必须返回一个 Boolean 类型的值，表示了该属性是否被成功删除。

```js
var p = new Proxy({ name: 'zhangsan', age: 10 }, {
    deleteProperty(target, key) {
        console.log(target, key)
        if (key === 'name') {
            console.log('属性' + key + '不可以被删除')
            return false
        }
        delete target[key]
        return true
    }
})
// true undefined
console.log(delete p.age, p.age)
// 属性name不可以被删除
// false zhangsan
console.log(delete p.name, p.name)
```

如果违背了以下不变量，proxy 将会抛出一个 TypeError:

- 如果目标对象的属性是不可配置的，那么该属性不能被删除

```js
var obj = {}
Object.defineProperty(obj, 'age', {
    configurable: false,
    value: 10
})
var p = new Proxy(obj, {
    deleteProperty(target, key) {
        delete target[key]
        return true
    }
})
// Uncaught TypeError: 'deleteProperty' on proxy: trap returned truish for property 'age' which is non-configurable in the proxy target
console.log(delete p.age, p.age)
```

对比vue2.x

> 其实与 `$set` 解决的问题类似，Vue2.0是无法监听到属性被删除的，所以提供了 `$delete` 用于删除属性，但是对于Proxy，是可以监听删除操作的，所以就不需要再使用 `$delete` 了

#### 4.5 `handler.apply(target, thisArg, argumentsList)`

- 函数调用操作的捕捉器。当proxy监听的是一个函数的时候，当调用这个函数时，会进入apply钩子函数
- `target` 目标对象（函数）
- `thisArg` 被调用时的上下文对象
- `argumentsList` 被调用时的参数数组
- `返回值` 可返回任何值

```js
var p = new Proxy(function() {}, {
    apply(target, thisArg, argumentsList) {
        return argumentsList[0] + argumentsList[1] + argumentsList[2]
    }
})
// 6
console.log(p(1, 2, 3))
```

如果违反了以下约束，代理将抛出一个TypeError：

- target必须是可被调用的。也就是说，它必须是一个函数对象。上面的代码中，将 `function(){}` 改成 `{}` 将会报错

#### 4.6 `handler.ownKeys`

当通过 `Object.getOwnPropertyNames`,`Object.getownPropertySymbols`,`Object.keys`,`Reflect.ownKeys`去获取对象的信息的时候，就会进入ownKeys这个钩子函数

#### 4.7 `handler.construct`

当使用new操作符的时候，会进入construct这个钩子函数

#### 4.8 `handler.defineProperty`

当使用Object.defineProperty去修改属性修饰符的时候，会进入这个钩子函数

#### 4.9 `handler.getPropertyOf`

当读取对象的原型的时候，会进入这个钩子函数

#### 4.10 `handler.setPropertyOf`

当设置对象的原型的时候，会进入这个钩子函数

#### 4.11 `handler.isExtensible`

当通过Object.isExtensible去判断对象是否可以添加新的属性的时候，进入这个钩子函数

#### 4.12 `handler.preventExtensions`

当通过Object.preventExtensions去设置对象不可以修改新属性时候，进入这个钩子函数

#### 4.13 `handler.getOwnPropertyDescriptor`

在获取代理对象某个属性的属性描述时触发该操作，比如在执行 Object.getOwnPropertyDescriptor(proxy, "foo") 时会进入这个钩子函数

### 5. Reflect

> 在上面，我们获取属性的值或者修改属性的值都是通过直接操作target来实现的，但实际上ES6已经为我们提供了在Proxy内部调用对象的默认行为的API,即Reflect。比如下面的代码

```js
const obj = {}
const proxy = new Proxy(obj, {
    get(target, key, receiver) {
        return Reflect.get(target, key, receiver)
    }
})
```

> 大家可能看到上面的代码与直接使用target[key]的方式没什么区别，但实际上Reflect的出现是为了让Object上面的操作更加规范，比如我们要判断某一个prop是否在一个对象中，通常会使用到in,即

```js
const obj = {name: 'zhangsan'}
console.log('name' in obj)
```

> 但上面的操作是一种命令式的语法，通过Reflect可以将其转变为函数式的语法，显得更加规范

```js
Reflect.has(obj,'name')
```

### 6. 参考文章

- [掘金博文](https://juejin.im/post/6861725116389130254)
- [MDN](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Proxy)
