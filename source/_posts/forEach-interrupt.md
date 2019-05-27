---
title: forEach遍历中断
date: 2019-05-22 17:28:36
tags:
---

```js
try {
    [22, 34, 20, 36, 98].forEach( (value, index, arr) => {
        if (value === 20) {
            console.log('遇到20能够退出循环吗？')
            throw Error('自定义异常提示语')
            return  // return并不能中断循环，这里可写可不写
        } else console.log('当前遍历值：', value)
    })
} catch(err) {
    console.log(err.message)
}
// 22
// 34
// 遇到20能够退出循环吗？
// 自定义异常提示语
```