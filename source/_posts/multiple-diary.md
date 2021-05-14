---
title: Diary【一】
date: 2019-05-22 17:43:43
toc: true
tags: [dairy]
---

### 1. setTimeout中方法体添加引号与不添加引号的区别

> `添加引号`的情况，调用的函数是`全局范围`的。`未添加引号`的情况，调用的是`局部范围`的。

<!-- More -->

```js
var testFn = function () {
    console.info('hello from global')
}

var fn = function () {
    var testFn = function () {
        console.info('hello fron inner')
    }

    setTimeout('testFn()', 1000)  // hello form global
    setTimeout(testFn, 1000)  // hello from inner

    // 在fn()函数内部没有定义testFn的时候，会向上寻找testFn，这里找到了全局函数，执行效果：
    setTimeout(testFn, 1000)  // hello from global
}

fn()
```

### 2. AMD/CMD规范 common.js

`common.js` 运行在`服务器端`，因为模块就在磁盘上，所以加载时间几乎可以忽略不计，属于同步加载。 [典型代表 `node.js`]
`AMD`与`CMD`都属于异步加载，运行在`浏览器端`。
`AMD` 推崇依赖前置，定义模块的时候就需要申明需要依赖的模块 [典型代表 `require.js`]
`CMD` 推崇依赖就近，需要用到的时候才去require相关模块 [典型代表 `sea.js`]

### 3. stopPropagation() 以及 stopImmediatePropagation()

`stopImmediatePropagation` 用于阻止冒泡，在执行完当前元素上的事件处理程序之后，停止当前元素以及后续元素的事件处理程序

`stopPropagation` 同样也是用于阻止冒泡，在执行完绑定到当前元素上的所有事件处理程序之后，停止后续元素的事件处理程序

同一个元素用`on`绑定多次同样事件时（例如click），最后一次会覆盖之前的事件，不会执行前面。而用`addEventListener`绑定多次同样事件时，都会执行。

```html
<div id="large" style="width: 150px; height: 150px; background: orange">
    <div id="middle" style="width: 100px; height: 100px; background: cadetblue">
        <div id="small" style="width: 50px; height:50px; background: coral"></div>
    </div>
</div>
```

```js
(function(){
    var large = document.getElementById('large')
    var middle = document.getElementById('middle')
    var small = document.getElementById('small')

    large.addEventListener('click', function(){
        alert('large div is clicked')
    })
    middle.addEventListener('click', function(){
        alert('middle div is clicked')
    })

    // 第一种情况： 点击small时，会依次弹出三个窗口，这是因为向上冒泡产生的
    small.addEventListener('click', function(){
     alert('small div is clicked')
    })

    // 第二种情况： 点击small时，会弹出四个窗口
    // small.addEventListener('click', function(){
    //  alert('small-1 is clicked')
    // })
    // small.addEventListener('click', function(){
    //  alert('small-2 is clicked')
    // })

    // 第三种情况，添加stopPropagation： 点击small时，会弹出两个窗口
    // small-1 is clicked    small-2 is clicked
    // small.addEventListener('click', function(event){
    //  alert('small-1 is clicked')
    //  event.stopPropagation()
    // })
    // small.addEventListener('click', function(){
    //  alert('small-2 is clicked')
    // })

    // 第四种情况，添加stopImmediatePropagation： 点击small时，会弹出一个窗口
    // small-1 is clicked
    // 能够很明显看到与stopPropagation的区别，调用stopImmediatePropagation之后，第二个定义的click事件也被取消而不被执行了。
    // small.addEventListener('click', function(event){
    //     alert('small-1 is clicked')
    //     event.stopImmediatePropagation()
    // })
    // small.addEventListener('click', function(){
    //     alert('small-2 is clicked')
    // })
})()
```

<div id="large" style="box-sizing: border-box; width: 150px; height: 150px; background: orange; cursor: pointer;"><!-- 
    --><div id="middle" style="box-sizing: border-box; width: 100px; height: 100px; background: cadetblue"><!-- 
        --><div id="small" style="box-sizing: border-box; width: 50px; height:50px; background: coral"></div><!-- 
    --></div>
</div>

<script>
    (function(){
        var large = document.getElementById('large')
        var middle = document.getElementById('middle')
        var small = document.getElementById('small')

        large.addEventListener('click', function(){
            alert('large div is clicked')
        })
        middle.addEventListener('click', function(){
            alert('middle div is clicked')
        })

        // 第一种情况： 点击small时，会依次弹出三个窗口，这是因为向上冒泡产生的
        small.addEventListener('click', function(){
        alert('small div is clicked')
        })

        // 第二种情况： 点击small时，会弹出四个窗口
        // small.addEventListener('click', function(){
        //  alert('small-1 is clicked')
        // })
        // small.addEventListener('click', function(){
        //  alert('small-2 is clicked')
        // })

        // 第三种情况，添加stopPropagation： 点击small时，会弹出两个窗口
        // small-1 is clicked    small-2 is clicked
        // small.addEventListener('click', function(event){
        //  alert('small-1 is clicked')
        //  event.stopPropagation()
        // })
        // small.addEventListener('click', function(){
        //  alert('small-2 is clicked')
        // })

        // 第四种情况，添加stopImmediatePropagation： 点击small时，会弹出一个窗口
        // small-1 is clicked
        // 能够很明显看到与stopPropagation的区别，调用stopImmediatePropagation之后，第二个定义的click事件也被取消而不被执行了。
        // small.addEventListener('click', function(event){
        //     alert('small-1 is clicked')
        //     event.stopImmediatePropagation()
        // })
        // small.addEventListener('click', function(){
        //     alert('small-2 is clicked')
        // })
    })()
</script>

### 4. js事件中的三个阶段

三个阶段分别为`捕获阶段` `目标阶段` `冒泡阶段`
element.addEventListener(type, fn[, useCapture])
useCapture - 指定事件句柄在捕获阶段或者冒泡阶段执行， 默认false(事件句柄在冒泡阶段执行)，设置为true(事件句柄在捕获阶段执行)。

- 捕获阶段
- 目标阶段
- 冒泡阶段

### 5. 常见的前端性能优化手段

- css sprites（雪碧图）
- 避免使用css表达式
- css样式表放在页面顶部，脚本放在页面底部
- 内联图片和脚本使用data:URL（base64编码）模式直接包含在页面中，无需进行http请求，缺点：ie不支持，图片太大不适用，使用base64会增加页面体积，浏览器不会缓存
- 使用内容分发网络CDN
- 开启GZIP压缩，启用负载均衡
- 开启keep alive减少与服务器链接次数 缺点：使服务器负载增大，也更容易遭受攻击
- 精简压缩代码 uglifyjs
- 避免重定向 常见的301 302
- 配置e-tags 和 expires头 使用缓存
- 按需加载资源

### 6. parseInt() 以及 parseFloat()

> parseInt(string[, radix])

- string如果以‘ox’或者'oX'开头，将以16进制进行解析
- radix 要解析的数字基数，介于2-36之间
- 如果不给值或者为0，则按照基数为10来计算
- 如果小于2或者大于36，则返回NaN
- 开头和结尾的空格是允许的
- 如果字符串的第一个字符不能转换成对应的数字，将返回NaN

```js
parseInt('0x12')  // 17
parseInt('0x12', 2)  // 0  => parseInt('0', 2)
parseInt('0x12', 10)  // 0  => parseInt('0', 16)
parseInt('8')  // => parseInt('8', 0) => parseInt('8', 10)  // 8
parseInt('8', 1)  // NaN
parseInt('8', 37)  // NaN
parseInt('a23')  // NaN
parseInt('2a3')  // 2
parseInt(' 23')  // 23
parseInt(' 23 ')  // 23
parseInt(' 2 3 ')  // 2
parseInt('11', 2)  // 3
parseInt(11, 2)  // 3
parseInt('1100', 2)  // 12
parseInt('21', 2)  // NaN 2超出了2进制最大值1，并且是第一个字符
parseInt('12', 2)  // 1 2同样超出了2进制最大值1，但是第一个字符可以转换成二进制
parseInt('66', 8) // 6*8^1 + 6*8^0 = 48 + 6 = 54
parseInt('12', 16) // 1*16^1 + 2*16^0 = 16 + 2 = 18
[0, 1, 2, 3].map(parseInt) //  [parseInt(0, 0), parseInt(1, 1), parseInt(2, 2), parseInt(3, 3)] => [0, NaN, NaN, NaN]
```

parseFloat()

> parseInt(string[, radix])  这里语法待修改

- 开头和结尾的空格是允许的
- 如果字符串的第一个字符不能转换成对应的数字，将返回NaN
- 如果在解析过程中遇到了正负号（+ 或 -）、数字 (0-9)、小数点，或者科学记数法中的指数（e 或 E）以外的字符，则它会忽略该字符以及之后的所有字符

### 7. charAt()、charCodeAt() 和 fromCharCode()

- charAt()

   - String.charAt(index)
   - 返回字符串中指定位置的字符, 索引值 index < 0 或超出了范围则返回一个空字符串

 ```js
'abc'.charAt(1)  // 'b'
'abc'.charAt(-2)  // ''
'abc'.charAt(5)  // ''
```

- charCodeAt()

   - String.charCodeAt(index)
   - 返回字符串中指定位置的字符的 unicode 数值 [0, 1114111]
   - 开头的 128 个 unicode 编码单元和 ASCII 字符编码一样
   - 索引值 index < 0 或超出了范围则返回一个 NaN, 大于 255 的是中文

```js
'AabcdD'.charCodeAt(0)  // 65
'AabcdD'.charCodeAt(1)  // 97
'AabcdD'.charCodeAt(-1)  // NaN
'AabcdD'.charCodeAt(8)  // NaN
'中国'.charCodeAt(1)  // 22269
```

- fromCharCode()

   - String.fromCharCode(num1, num2, num3, ...)
   - 根据指定的 unicode 编码中的序号值来返回字符串

```js
String.fromCharCode(65, 66, 97)  // 'ABa'
```

### 8. 原生ajax

```js
var xhr = new XMLHttpRequest()
//  请求方式  请求路径  是否异步发送请求
xhr.open('GET', 'http://www.jnathan.cn/jnathan/effect/authKit.js', true)
xhr.send('hello world') 

xhr.onreadystatechange = function () {
    if (xhr.readyState == 4 && xhr.status == 200) {
        // alert(xhr.responseText)
    }
}
```

### 9. 有一个长度为n的数组，假定全部为整数，其中有且仅有一个数出现了奇数次，其他的数字都出现偶数次，找出出现了奇数次的那个数字。

> 时间复杂度为O(n) 空间复杂度O(1)
> 利用了`n ^ n = 0`, `n ^ 0 = n`两个等式的原理。
> 循环对数组数字进行异或计算，即  `0 ^ arr[0] ^ arr[1] ^ ... ^ arr[n - 1]` ，异或计算满足交换律原则，如果数组元素出现了相同的情况，可以移动到一起先进行计算，如果出现了`偶数次`，其结果为  `0`，出现了奇数次的数其结果为该数字，所以表达式最后得出来的结果即为数组所有`出现了奇数次元素`的`异或操作`的结果。比如：[1,2,2,3,4,4]进行异或操作得到的结果是 `1 ^ 3 = 2` ，特殊情况下（只有一个数字出现了`奇数次`），那么最后的结果就是出现了`奇数次`的那个数字！！

```js
function fn7 (arr) {
    var oddTimesNumber = 0
    for (let i in arr) {
        oddTimesNumber = oddTimesNumber ^ arr[i]
    }

    return oddTimesNumber
}
fn7([1,2,3,4,5,4,4,3,2,1,1,1])  // 3
```

### 10. 递归求1-10的和

```js
function getSum(nextArr, curSum = 0) {

    var sum = nextArr[0] + curSum

    if (nextArr.length == 1) {
        return sum
    } else {
        return getSum(nextArr.slice(1), sum)
    }

}
getSum([1,2,3,4,5,6,7,8,9,10])
```

### 11. 去除数组重复元素

- 直接双层循环比较

```js
function unique (arr) {

    var result = []
    var isRepeat

    for (var i = 0; i < arr.length; i++) {

        isRepeat = false  // 默认不重复，进行push操作，当判断重复时不操作
        for (var j = 0; j < result.length; j++) {
            if (arr[i] === result[j]) {
                isRepeat = true
                break
            }
        }

        if (!isRepeat) {
            result.push(arr[i])
        }
    }

    return result

}
unique([1,1,2,3,4])
```

- 利用`indexOf()`， 但是`indexOf`会一直循环到找到目标为止，浪费了时间

```js
function unique (arr) {

    var result = []

    for (var i = 0; i < arr.length; i++) {
        if (result.indexOf(arr[i]) === -1) {
            result.push(arr[i])
        }
    }

    return result
}
unique([1,1,2,3,4])
```

- 最快的方法 把已经出现过的元素通过下标的形式存入一个`Object`内。下标的引用的实现原理利用的是哈希算法，要比用`indexOf()`搜索数组快的多。由于多了一个`hash`表，占用的内存会更多，空间换时间做法

```js
function unique (arr) {
    // hashObj为hash表，result为临时数组
    var hashObj = {}, result = []
    for (var i = 0; i < arr.length; i++) {
        // 如果hash表中没有当前项
        if (!hashObj[arr[i]]) {
            // 存入hash表
            hashObj[arr[i]] = true
            // 把当前数组的当前项push到临时数组里面
            result.push(arr[i])
        }
    }
    return result
}
```

- 折衷方法 时间上比`indexOf`快，比`hash`表的方式要慢。先按照从小到大的顺序排序，然后比较相邻的两个值大小
  
```js
function unique (arr) {
    arr.sort()
    var result = [arr[0]]
    for (var i = 1; i < arr.length; i++) {
        if (arr[i] !== result[result.length - 1]) {
            result.push(arr[i])
        }
    }
    return result
}
```

- `es6`的`filter()`方法

```js
function unique (arr) {
    return arr.filter( (value, index) => { 
        arr.indexOf(value) === index
        console.log(arr.indexOf(value), index)
        // 0 0
        // 0 1
        // 2 2
        // 3 3
        // 4 4
        // 5 5
        // 2 6
    })
}
unique([1,1,2,3,4,5,2])
```

### 12. jquery  里面的  $(document).ready(function(){})  和  window.onload  的区别

- `window.onload`需要等到页面中包括图片在内的元素全部加载完毕才能执行；`ready()`是DOM元素绘制结束之后就执行，不用等到加载完毕。
- `window.onload`如果定义了多次，则只会执行一个，后者会覆盖前者； `ready()`可以同时编写多个，并且都会执行。
- `window.onload`没有简化写法；`$(document).ready(function(){})`可以简化成`$(function(){})`。
- 补充：`$(window).load(function(){})`和 `window.onload()`效果一致。

### 13. 渐进增强与优雅降级

- 渐进增强 一开始就针对低版本浏览器进行构建页面，完成基本的功能，然后再对高级浏览器进行效果、交互的优化，达到更好的体验。
- 优雅降级 一开始就构建站点的完整功能，然后针对浏览器进行测试和修复。比如一开始使用`CSS3`的特性构建了一个应用，然后逐步针对各大低级浏览器进行`hack`使其可以在低版本浏览器上正常浏览。

### 14. cookies、localStorage和sessionStorage的区别

- cookies存在于客户端，始终会在http请求中通过  Request Headers  Cookie头携带，即使当前请求不需要使用到cookie；大小一般在4KB，所以只适合保存很小的数据，比如会话标识；cookies在没有设置过期时间（expires）的时候，默认在关闭浏览器之后就会被清除，在有设置过期时间的时候，会一直持续到过期时间有效（即使关闭当前窗口或者关闭浏览器）。
- localStorage 和 sessionStorage不会发送数据到服务端，仅在本地保存；也有大小的限制，但一般在5M左右；localStorage一直有效，除非手动清除； sessionStorage只在浏览器关闭之前有效。

另外： localStorage 和 cookies 在同源的浏览器窗口之间是共享的，sessionStorage不共享，因此至少有以下两种方法实现不同页面间的通信：

- 利用localStorage

```js
// A页面
$('#btn').click(function(){
    localStorage.setItem('name', 'Jnathan')
})

// B页面
window.addEventListener('storage', function(event){
    console.log(event.key + '=' + event.newValue)
})
```

- 利用cookie + setInterval()

```js
// A页面
$('#btn').click(function () {
    document.cookie = "name=" + 'Jnathan'
})

// B页面
function getKey(key) {  
    return JSON.parse("{\""+ document.cookie.replace(/;\s+/gim,"\",\"").replace(/=/gim, "\":\"") +"\"}")[key];  
}
setInterval(function(){
    console.log('name:', getKey('name'))
}, 1000)
```

### 15. new 操作符的四个步骤

```js
var Fn = function() {}
var fn = new Fn()
```

- 创建一个`空对象`

```js
var obj = new Object()
```

- 设置`obj`的`原型`，将`obj`的`原型`指向`Fn`的`原型链`

```js
obj.__proto__ = Fn.prototype
```

- 将`Fn`中的`this`指向`obj`,并执行`Fn`

```js
var result = Fn.call(obj)
```

- 判断`Fn`的`返回值类型`。如果是`引用类型，则返回这个`引用类型的对象`；如果是`值类型`，则返回`obj`

```js
if (typeof result == 'object') {  // 引用类型
    fn = result
} else { // 值类型
    fn = obj
}
```

### 16. 构造函数实现继承的几种方式

```js
function Animal () {
    this.species = '动物'
}
function Cat (name, color) {
    this.name = name
    this.color = color
}
```

使猫继承动物：

- call apply

```js
function Cat (name, color) {
    Animal.apply(this, arguments)
    this.name = name
    this.color = color
}

var cat1 = new Cat('name1', 'color1')
alert(cat1.species) //  动物
```

- prototype（其一）

```js
function Cat (name, color) {
    Animal.apply(this, arguments)
    this.name = name
    this.color = color
}

var cat1 = new Cat('name1', 'color1')
alert(cat1.species) //  动物
```

- prototype（其二）

```js
function Animal () {}
Animal.prototype.species = '动物'

Cat.prototype = Animal.prototype
Cat.prototype.constructor = Cat

var cat3 = new Cat('name3', 'color3')
alert(cat3.species) // 动物   
```

- 利用空对象作为中介 上面那种方法的缺点：Cat.prototype和Animal.prototype现在指向了同一个对象，那么任何对Cat.prototype的修改，都会反映到Animal.prototype。

```js
var Fn = function () {}
Fn.prototype = Animal.prototype
Cat.prototype = new Fn()
Cat.prototype.construvtor = Cat
```

将其封装成一个函数

```js
function extend (Child, Parent) {
    var Fn = function () {}
    Fn.prototype = Parent.prototype
    Child.prototype = new Fn()
    Child.prototype.constructor = Child
}

extend(Cat, Animal)
var cat4 = new Cat('name4', 'color4')
alert(cat4.species) // 动物
```

- 拷贝继承

```js
function Animal () {}
Animal.species = '动物'

function extend2 () {
    var C = Child.prototype
    var P = Parent.prototype

    for (var i in P) {
        C[i] = P[i]
    }
}

extend2(Cat, Animal)
var cat5 = new Cat('name5', 'color5')
alert(cat5.species) // 动物
```

### 17. 非构造函数的继承

```js
var Chinese = {
    nation: '中国'
}
var Doctor = {
    career: '医生'
}
```

- 浅拷贝

```js
function shallowCopy (Parent) {
    var Child = {}

    for (var i in Parent) {
        Child[i] = Parent[i]
    }

    return Child
}

var doctor1 = shallowCopy(Chinese)
alert(doctor1.nation) // 中国
```

- 深拷贝

```js
function deepCopy (Child, Parent) {
    var Child = Child || {}

    for (var i in Parent) {
        if (typeof Parent[i] == 'object') {
            Child[i] = (Parent[i].constructor === Array) ? [] : {}
            deepCopy(Child[i], Parent[i])
        } else {
            Child[i] = Parent[i]
        }
    }

    return Child
}

var doctor2 = deepCopy(Doctor, Chinese)
alert(doctor.nation) // 中国
```

### 18. [参考链接](http://www.ruanyifeng.com/blog/2010/05/object-oriented_javascript_inheritance.html)