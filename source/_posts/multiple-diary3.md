---
title: Diary【三】
date: 2021-04-08 20:32:25
tags: diary
---

### new的模拟实现

实现以下效果

```js
function Person() {
    // ...
}

// 使用new
var person = new Person(params)
// 使用自定义的objectFactory
var person = objectFactory(Person, params)
```

<!-- More -->

构造函数没有返回值的情况

```js
function Person(name, age) {
    this.name = name
    this.age = age
}

function objectFactory() {
    var obj = {}
    var Contructor = [].shift.call(arguments)
    obj.__proto__ = Contructor.prototype
    Contructor.apply(obj, arguments)
    return obj
}

var person = objectFactory(Person, 'Wukong', 500)
console.log(person.name)  // Wukong
console.log(person.age)  // 500
```

构造函数有返回值的情况

#### 返回基本类型

```js
function Person(name, age) {
    this.name = name
    this.age = age
    return 'hello everyone'
}
var person = new Person('Wukong', 500)
console.log(person.name)  // Wukong
console.log(person.age)  // 500
```

#### 返回对象

```js
function Person(name, age) {
    this.name = name
    this.age = age
    return {
        name: 'zhangsan',
        gender: 'female'
    }
}
var person = new Person('Wukong', 500)
console.log(person.name)  // zhangsan
console.log(person.age)  // undefined
console.log(person.gender)  // female
```

结合以上两种情况，对objectFactory方法进行更改，最终结果如下

```js
function Person(name, age) {
    this.name = name
    this.age = age
    return {
        name: 'zhangsan',
        gender: 'female'
    }
}

function objectFactory() {
    // 创建一个空对象
    var obj = {}
    // 将类数组参数转为数组，并调用数组的shift方法 弹出第一个参数 此例为构造函数Person
    var Contructor = [].shift.call(arguments)
    // 将实例对象的__proto__属性指向构造函数的prototype原型 从而实现继承
    obj.__proto__ = Contructor.prototype
    // 执行构造函数 传递参数 改变this指向
    var returns = Contructor.apply(obj, arguments)
    // 判断返回的值类型 如果是对象，返回这个对象；如果没有，则返回创建的obj
    return typeof returns === 'object' ? returns : obj
}

var person = objectFactory(Person, 'Wukong', 500)
console.log(person.name)  // zhangsan 
console.log(person.age)  // undefined
console.log(person.gender)  // female
```