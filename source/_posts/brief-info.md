---
title: 知识点
date: 2019-06-06 16:48:44
tags:
---

### 1. 一些js插件为什么以`;`开头

因为js代码允许在结束的时候不添加`;`, 所以两个js文件在合并拼接的的时候可能出错。

```js
// a.js
// 末尾没有添加;符号
var a = 'test'

// b.js
(function(){
    // do something
})();

```

<!-- More -->

以上两个文件进行合并压缩的时候就可能变为：

```js
var a = 'test'(function(){})();

// 这样会报错： TypeError: 'test' is not a function
```

### 2. 使用闭包定义私有属性

```js
function Product() {

    var name;

    this.setName = function(value) {
        name = value;
    };

    this.getName = function() {
        return name;
    };
}

var p = new Product();
p.setName("test");

console.log(p.name); // 输出undefined
console.log(p.getName()); // 输出test
```

代码中，对象p的的`name`属性为`私有属性`，使用`p.name`不能直接访问。

### 3. 枚举属性

- 可枚举性
在js中，`基本数据类型`的原型属性是不可枚举的，比如`Object`、`Array`、`Number`等。

```js
    // 打印结果为空 Number内置属性是不可枚举的
    var num = new Number()
    for (let i in num) {
        console.log(i, num[i])
    }
```

- propertyIsEnumerable

> Object.propertyIsEnumerable(prop)
> prop 需要测试的属性名
> 返回值 用来表示指定的属性名是否可枚举的Boolean
> 属性必须满足 自身属性 && 枚举属性，才会返回true

```js
var obj = {}
obj.test = 'Jnathan'

var arr = [23, 67]
obj.propertyIsEnumerable('test')  // true
arr.propertyIsEnumerable(0)  // true
arr.propertyIsEnumerable(1)  // true
```

- hasOwnProperty

> Object.hasOwnProperty(prop)
> prop 需要测试的属性名
> 返回值 用来表示指定的属性名是否在对象中的Boolean

```js
var obj = {}
obj.x = 'Jnathan'
obj.y = function() {}
obj.hasOwnProperty('x')  // true
obj.hasOwnProperty('y')  // true 方法也属于属性
obj.hasOwnProperty('z')  // false 属性不存在
obj.hasOwnProperty('toString')  // false toString是继承的Object中的属性，obj自身属性中不包含
```

```js
function Person() {
    this.name = 'Jnathan'
}
Person.prototype = {
    constructor: Person,
    job: 'student',
}
var Jnathan = new Person()
Object.defineProperty(Jnathan, "gender", {
    value: 'female',
    enumerable: false
})
```

以上， 用`defineProperty`为`对象Jnathan`定义了一个名为`gender`的`不可枚举属性`

- 枚举 for...in

```js
for (let item in Jnathan) {
    console.log('Jnathan.' + item + ' = ' + Jnathan[item])
}
// Jnathan.name = Jnathan
// Jnathan.constructor = function Person() {
//     this.name = 'Jnathan'
// }
// Jnathan.job = student

function enumer() {
    this.a = '我是对象本身就有的属性'
}
enumer.prototype.b = '我是通过原型对象挂载的属性'
let fn = new enumer()
Object.defineProperty(fn, 'c', {
    value: '我是通过Object.defineProperty方法添加的可枚举属性',
    enumerable: true
});
for(let item in fn) {
    console.log(item)
}
// a
// b
// c
```

- 枚举 Object.keys()

```js
Object.keys(Jnathan)
// ["name"]

function enumer() {
    this.a = '我是对象本身就有的属性'
}
enumer.prototype.b = '我是通过原型对象挂载的属性'
let fn = new enumer()
Object.defineProperty(fn, 'c', {
    value: '我是通过Object.defineProperty方法添加的可枚举属性',
    enumerable: true
});
console.log(Object.keys(fn))
// ["a", "c"]
```

- 枚举 JSON.stringify()

```js
JSON.stringify(Jnathan)
// "{"name":"Jnathan"}"

function enumer() {
    this.a = '我是对象本身就有的属性'
}
enumer.prototype.b = '我是通过原型对象挂载的属性'
let fn = new enumer()
Object.defineProperty(fn, 'c', {
    value: '我是通过Object.defineProperty方法添加的可枚举属性',
    enumerable: true
})
console.log(JSON.stringify(fn))
// {"a":"我是对象本身就有的属性","c":"我是通过Object.defineProperty方法添加的可枚举属性"}
```

> 根据现象有以下总结
> for...in 可以枚举对象本身具有的属性，通过Object.defineProperty()方法加的可枚举属性，或者通过原型对象绑定的可以枚举属性
> Object.keys() 可以枚举对象本身的属性和通过Object.defineProperty()添加的可枚举属性
> JSON.stringify() 可以枚举对象本身的属性和通过Object.defineProperty()添加的可枚举属性，并序列化为JSON字符串

- !== undefined

```js
var obj = {x: 1, y: undefined, z: null}
console.log(obj.x !== undefined)  // true 属性存在
console.log(obj.y !== undefined)  // false 此时会出现歧义，不能准确判断属性是不存在还是属性值本身就为undefined
console.log(obj.z !== undefined)  // true 属性存在
console.log(obj.z != undefined)  // false != 不能区分undefined和null，将两者同等对待
console.log(obj.w !== undefined)  // false 属性不存在
console.log(obj.toString !== undefined)  // true 存在toString函数属性。
```

- in 自身存在的属性或者继承的属性均返回true

```js
var obj = {x: 1, y: undefined, z: null}
console.log("x" in obj)  // true 属性存在
console.log("y" in obj)  // true 属性存在
console.log("z" in obj)  // true 属性存在
console.log("w" in obj)  // false 属性不存在
console.log("toString" in obj)  // true 属性存在
```

- Object.defineProperty()

> 参数：
> Object.defineProperty(object, propertyname, descriptor)
> object 必需。 要在其上添加或修改属性的对象。 这可能是一个本机 JavaScript 对象（即用户定义的对象或内置对象）或 DOM 对象。
> propertyname 必需。 一个包含属性名称的字符串。
> descriptor 必需。 属性描述符。 它可以针对数据属性或访问器属性。
> 返回值：
> 已修改对象

当对象不具有指定的属性名称时，向对象添加新属性。当对象已具有指定的属性名称时，修改现有属性的特性。

```js
var obj = {}
Object.defineProperty(obj, "newDataProperty", {
    value: 101,
    writable: false,
    enumerable: false,
    configurable: true
});

obj.newDataProperty = 102
console.log(obj.newDataProperty)  // 101 whiteable为false 只读，不能修改
```

- Object.getOwnPropertyNames

由于obj的属性newDataProperty是不可枚举的，使用for...in不能找到newDataProperty属性

```js
for (let item in obj) {
    console.log(item, obj[item])  // 无输出
}
```

此时可以使用Object.getOwnPropertyNames来遍历

```js
var names = Object.getOwnPropertyNames(obj)
for (let i = 0; i < names.e]length; names++) {
    console.log(names, i, names[i])
}
// ["newDataProperty"] 0 newDataProperty
```

```js
var obj = {}
Object.defineProperty(obj, "newAccessorProperty", {
    set: function (x) {
        console.log("in property set accessor");
        this.newaccpropvalue = x;
    },
    get: function () {
        console.log("in property get accessor");
        return this.newaccpropvalue;
    },
    enumerable: true,
    configurable: true
});

obj.newAccessorProperty = 30
console.log("Property value: " + obj.newAccessorProperty)
// in property set accessor
// in property get accessor
// Property value: 30
```

- 修改DOM上的属性

```js
Object.getOwnPropertyDescriptor(Element.prototype, "querySelector")
// {value: ƒ, writable: true, enumerable: true, configurable: true}
```

```js
var descriptor = Object.getOwnPropertyDescriptor(Element.prototype, "querySelector")

descriptor.value = "query"
descriptor.writable = false

Object.defineProperty(Element.prototype, "querySelector", descriptor)

var elem = document.getElementById("div")

elem.querySelector = "anotherQuery"  // whitable 为false 更改不生效
console.log(elem.querySelector)  // query
```
