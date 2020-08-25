---
title: == 和 ===
date: 2020-04-29 11:10:04
tags: [==, ===, 操作符, 转换]
---

先来几个例子，带着问题一起思考：
```js
console.log([10] == 10);                // true
console.log('10' == 10);                // true
console.log([] == 0);                   // true
console.log(true == 1);                 // true
console.log([] == false);               // true
console.log(![] == false);              // true
console.log([] == ![]);                 // true
console.log('' == 0);                   // true
console.log('' == false);               // true
console.log(null == false);             // false
console.log(null == true);              // false
console.log(!null == true);             // true
console.log(null == undefined);         // true
```

<!-- More -->

### 1. === (strict equality comparision)

```js
if (Type(x) !== Type(y))  return false
if (Type(x) === Type(y)) {
    if (Type(x) === Undefined) return true
    if (Type(x) === Null) return true
    if (Type(x) === String) {
        return 长度和对应位置上字符都相同 ? true : false
    }
    if (Type(x) === Boolean) {
        return xy同时为true或false ? true : false
    }
    if (Type(x) === Symbol) {
        return 同一个Symbol ? true : false
    }
    if (Type(x) === Number) {
        if (x === NaN) return false
        if (y === NaN) return false
        if (xy相等 例如 x = 8, y = 8) return true
        if (x = 0, y = -0) return true
        if (x = -0, y = 0) return true
        return false  // 其他情况均返回false
    }
}
```

### 2. == (abstract equality comparision)

```js
if (Type(x) === Type(y)) return x === y
if (Type(x) !== Type(y)) {
    if (x === null && y === undefined) return true
    if (x === undefined && y === null) return true
    if (Type(x) === Number && Type(y) === String) return x == ToNumber(y)
    if (Type(x) === String && Type(y) === Number) return ToNumber(x) == y
    if (Type(x) === Boolean) return ToNumber(x) == y
    if (Type(y) === Boolean) return x == ToNumber(y)
    if ([String, Number, Symbol].includes(Type(x)) && Type(y) === Object) return x == ToPrimitive(y)
    if (Type(x) === Object && [String, Number, Symbol].includes(Type(y))) return ToPrimitive(x) == y
    return false  // 其他情况均返回false
}
```

- Type(x) 获取x的类型
- ToNumber(x) 将x转换成Number类型
- ToBoolean(x) 将x转换成Boolean类型
- ToString(x) 将x转换成String类型
- ToPrimitive(x) 将x转换成原始值

### 3. ToPrimitive()方法 - <small>*ECMAScript规范中的抽象方法，Javascript不能访问*</small>

先了解js中的原始类型：
- Null: null
- Undefined: undefined
- Number: 如 0、8、5.5、NaN、Infinite
- Boolean: true、false
- String: 如 'abc'、''
- 其余都属于'非原始类型'，如 Array、Object、Function

> typeof null 得到的结果是object, 这是js在设计之初的问题。但null是属于原始类型的（记住就行了🐶）

> ToPrimitive(input[, hint])转换为原始类型的方法，根据preferedType目标类型进行转换。preferedType只能是 `string` 或 `number`

转换步骤：
- `hint` 为 `number`
  - input是原始值，返回input本身
  - input是对象
    - 先判断input.valueOf()的返回值。如果是原始值，返回结果。
    - 否则，再判断input.toString()的返回值。如果是原始值，返回结果。
    - 否则，抛出不能转换成原始值的TypeError。
- `hint` 为 `string`
  - input是原始值，返回input本身
  - input是对象
    - 先判断input.toString()的返回值。如果是原始值，返回结果。
    - 否则，再判断input.valueOf()的返回值。如果是原始值，返回结果。
    - 否则，抛出不能转换成原始值的TypeError。
- `hint` 为空
  - `input` 是 `Date` 日期类型
    - input被认为是string。
    - 先判断input.toString()的返回值。如果是原始值，返回结果。
    - 否则，再判断input.valueOf()的返回值。如果是原始值，返回结果。
    - 否则，抛出不能转换成原始值的TypeError。
  - `input` 是 `非Date` 类型
    - input被认为是number。
    - 先判断input.valueOf()的返回值。如果是原始值，返回结果。
    - 否则，再判断input.toString()的返回值。如果是原始值，返回结果。
    - 否则，抛出不能转换成原始值的TypeError。

> 当调用ToPrimitive而没有传hint参数时，默认情况下hint将被赋值Number。但是可以重写对象的@@toPrimitive方法来覆盖这个行为。在本规范中，只有Date和Symbol两个对象重写了默认的ToPrimitive操作。对于Date对象，如果没有传hint，hint将被默认赋值为String


示例1：ToPrimitive([10])
```js
// 没有传入hint参数，并且input是Array, 所以hint被赋值为Number
// 先判断valueOf()
[10].valueOf() 等于 [10]，属于 Array/Object，不是原始类型
// 再判断toString()
[10].toString() 等于 '10'，属于 String，是原始类型
所以 return '10'
```

示例2：ToPrimitive(function sayHi(){})
```js
var sayHi = function (name) {
    console.log('hi, ' + name)
}
// 没有传入hint参数，并且input是Function, 所以hint被赋值为Number
// 先判断valueOf()  typeof sayHi.valueOf === 'function'
sayHi.valueOf() 等于 f (name) { console.log('hi, ' + name) } 属于Function，不是原始类型
// 再判断toString()  typeof sayHi.toString === 'string'
sayHi.toString() 等于 function (name) { console.log('hi, ' + name) } 属于String，是原始类型
所以 return sayHi方法的方法体源码字符串
```

上面示例中 [10] == 10
- Type(x) === Object, Type(y) === Number，变成比较 ToPrimitive([10]) == 10
- 由示例1 知道 ToPrimitive([10]) === '10'，变成比较 '10' == 10
- 此时，Type(x) === String, Type(y) === Number，变成比较 ToNumber('10') == 10
- ToNumber('10') === 10，变成比较 10 == 10
- return true

[] == 0
- Type(x) === Object, Type(y) === Number, 变成比较 ToPrimitive([]) == 0
- 没有传入hint参数，[]不属于Date类型，hint被赋值Number
- Number hint先判断 valueOf()， [].valueOf()返回数组本身，不是原始类型
- 再判断toString()，[].toString() === ''，属于原始类型 return ''
- 等式变成 '' == 0
- 此时，Type(x) === String, Type(y) === Number, 变成比较ToNumber('') == 0
- ToNumber('') === 0, 变成比较 0 == 0
- return true

按照上述步骤推导，不难可以得到： [] == false 返回true
那么 ![] == false 应该返回 false吧！ 在控制台验证一下 `![] == false` 返回true。纳尼！！

- ![] 先转换成 Boolean。 Boolean([]) === true, ![] === false
- 此时变成 false == false return true

由 [] == false 返回true 和 ![] == false 返回true 得到 [] == ![] 返回 true。 🐶

null == false 走的是最后一步，不满足任何条件，直接返回false，这一说法没有理解。难道不是Type(false) === Boolean然后比较 null == 0么。。。 再就是ToPrimitive(null) == 0 ?? 炸裂，我太难了。

### 4. toString()方法

> 可以将除(null和undefined)之外的数据类型转为字符串

```js
[].toString()  // ""
[3, 8].toString()  // "3, 8"
new Date().toString()  // Fri May 01 2020 14:24:34 GMT+0800 (中国标准时间)"
/abc/ig.toString()  // "/abc/ig"

(16).toString()  // '16'
(16).toString(2)  // '10000' 二进制(2-35都可)
(16).toString(8)  // '20' 八进制
(16).toString(16)  // '10' 十六进制
0.000006.toString()  // '0.000006'
0.0000006.toString() // '6e-7' 小数点后面6个或以上0时 小数采用e表示法输出
(1234567890123456789012).toString()  // '1.2345678901234568e+21' 整数部分超过21位，采用e表示法
```

可以用toString()区分自定义方法和原生方法
```js
function fn () {}
fn.toString()  // "function fn () {}"  -- fn 是自定义方法
Number.toString()  // "function Number() { [native code] }" -- Number是一个原生方法
```

还可以用Object.prototype上的原生toString()方法判断数据类型
```js
Object.prototype.toString.call('abc');  // "[object String]"
Object.prototype.toString.call(123);  // "[object Number]"
Object.prototype.toString.call(true);  // "[object Boolean]"
Object.prototype.toString.call(null)  // "[object Null]"
Object.prototype.toString.call(undefined)  //”[object Undefined]"
```

判断原生引用类型
```js
// 函数类型
function fn(){console.log('test')}
Object.prototype.toString.call(fn)  // "[object Function]"

// 日期类型
var date = new Date()
Object.prototype.toString.call(date)  // "[object Date]"

// 数组类型
var arr = [1,2,3]
Object.prototype.toString.call(arr)  // "[object Array]"

// 正则表达式
var reg = /[hbc]at/gi
Object.prototype.toString.call(reg)  // "[object RegExp]"

// 自定义类型
function Person(name) {
    this.name = name
}
var person = new Person("Rose")
Object.prototype.toString.call(person)  // "[object Object]"
```

### 5. String()方法

- 如果有toString()方法，则调用该方法，并返回结果
- 否则：
  - 如果是null，返回"null"
  - 如果是undefined，返回"undefined"

要区分toString()和String()
- toString(radix)支持传入radix进制参数
- toString不支持null和undifined调用
- String(null) === 'null'
- String(undifined) === 'undefined'

### 6. Number()方法

- 布尔值：true - 1 false - 0
- 数字值：返回数字本身
- null：返回0
- undefined: 返回NaN
- 字符串：
  - 只包含数字：转换成十进制，忽略前导0
  - 包含有效的浮点格式：转换成浮点数职，忽略前导0
  - 空字符串：返回0
  - 其他情况：返回NaN
- 对象：调用对象的valueOf()方法，然后依据前面的规则转换返回的值。如果转换的结果是NaN，则调用对象的toString()方法，再次依照前面的规则转换返回的字符串值。

### 7. Boolean()方法

以下值会被转换为false：`false`、`""`、`0`、`-0`、`NaN`、`null`、`undefined`，其余任何值都会被转换为`true`。
另外Boolean()不传递参数时，也返回false。

### 8. 隐式转换

在某些情况下，即使我们不提供显示转换，Javascript也会进行自动类型转换，主要情况有：

#### 8.1 用于检测是否为非数值的函数：isNaN(mix)

isNaN()函数，会尝试将参数值用Number()进行转换，如果结果为“非数值”则返回true，否则返回false。

#### 8.2 递增递减操作符（包括前置和后置）、一元正负符号操作符

这些操作符适用于任何数据类型的值，针对不同类型的值，该操作符遵循以下规则（经过对比发现，其规则与Number()规则基本相同）：

- 如果是包含有效数字字符的字符串，先将其转换为数字值（转换规则同Number()），在执行加减1的操作，字符串变量变为数值变量。
- 如果是不包含有效数字字符的字符串，将变量的值设置为NaN，字符串变量变成数值变量。
- 如果是布尔值false，先将其转换为0再执行加减1的操作，布尔值变量编程数值变量。
- 如果是布尔值true，先将其转换为1再执行加减1的操作，布尔值变量变成数值变量。
- 如果是浮点数值，执行加减1的操作。
- 如果是对象，先调用对象的valueOf()方法，然后对该返回值应用前面的规则。如果结果是NaN，则调用toString()方法后再应用前面的规则。对象变量变成数值变量。

#### 8.3 加法运算操作符

- 如果两个操作值都是数值，其规则为：
  - 如果一个操作数为NaN，则结果为NaN
  - 如果是Infinity+Infinity，结果是Infinity
  - 如果是-Infinity+(-Infinity)，结果是-Infinity
  - 如果是Infinity+(-Infinity)，结果是NaN
  - 如果是+0+(+0)，结果为+0
  - 如果是(-0)+(-0)，结果为-0
  - 如果是(+0)+(-0)，结果为+0
- 如果有一个操作值为字符串，则：
  - 如果两个操作值都是字符串，则将它们拼接起来
  - 如果只有一个操作值为字符串，则将另外操作值转换为字符串，然后拼接起来
  - 如果一个操作数是对象、数值或者布尔值，则调用toString()方法取得字符串值，然后再应用前面的字符串规则。对于undefined和null，分别调用String()显式转换为字符串。

可以看出，加法运算中，如果有一个操作值为字符串类型，则将另一个操作值转换为字符串，最后连接起来。

#### 8.4 乘除、减号运算符、取模运算符

这些操作符针对的是运算，所以他们具有共同性：如果操作值之一不是数值，则被隐式调用Number()函数进行转换。具体每一种运算的详细规则请参考ECMAScript中的定义

#### 8.5 逻辑操作符（!、&&、||）

逻辑非（！）操作符首先通过Boolean()函数将它的操作值转换为布尔值，然后求反。

- 逻辑与（&&）操作符，如果一个操作值不是布尔值时，遵循以下规则进行转换：
  - 如果第一个操作数经Boolean()转换后为true，则返回第二个操作值，否则返回第一个值（不是Boolean()转换后的值）
  - 如果有一个操作值为null，返回null
  - 如果有一个操作值为NaN，返回NaN
  - 如果有一个操作值为undefined，返回undefined
- 逻辑或（||）操作符，如果一个操作值不是布尔值，遵循以下规则：
  - 如果第一个操作值经Boolean()转换后为false，则返回第二个操作值，否则返回第一个操作值（不是Boolean()转换后的值）
  - 对于undefined、null和NaN的处理规则与逻辑与（&&）相同

#### 8.6 关系操作符（<, >, <=, >=）

- 如果两个操作值都是数值，则进行数值比较
- 如果两个操作值都是字符串，则比较字符串对应的字符编码值
- 如果只有一个操作值是数值，则将另一个操作值转换为数值，进行数值比较
- 如果一个操作数是对象，则调用valueOf()方法（如果对象没有valueOf()方法则调用toString()方法），得到的结果按照前面的规则执行比较
- 如果一个操作值是布尔值，则将其转换为数值，再进行比较

注：NaN是非常特殊的值，它不和任何类型的值相等，包括它自己，同时它与任何类型的值比较大小时都返回false。

#### 8.7 相等操作符

相应规则见文章最初始。

### 9. 位运算符

#### 9.1 按位与(&)

#### 9.2 按位与(|)

#### 9.3 按位非(~)

#### 9.4 按位异或(^)

#### 9.5 左移(<<)

> 该操作符会将左边操作数向左移动右边操作数的位数。向左被移出的位被丢弃，右侧用 0 补充。
> 3 << 2 = 3 * 2 ^ 2 = 12

#### 9.6 有符号右移(<<)

> 该操作符会将左边操作数向右移动右边操作数的位数。向右被移出的位被丢弃，拷贝最左侧的位以填充左侧。由于新的最左侧的位总是和以前相同，符号位没有被改变。所以被称作“符号传播”。
> 12 >> 2 = 12 / 2 ^ 2 = 3

#### 9.6 无符号右移(<<<)

> 该操作符会将左边操作数向右移动右边操作数的位数。向右被移出的位被丢弃，左侧用0填充。因为符号位变成了 0，所以结果总是非负的。（译注：即便右移 0 个比特，结果也是非负的。）

### 10. 参考文章

- [文章一](https://juejin.im/entry/584918612f301e005716add6)
- [文章二](https://blog.csdn.net/huang100qi/article/details/80543045)
- [文章三](https://www.cnblogs.com/Renyi-Fan/p/9189441.html#_labelTop)