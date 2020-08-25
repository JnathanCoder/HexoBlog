---
title: Web Worker 简介
date: 2020-08-13 16:22:26
tags: [worker, web-worker]
---

### 背景

> JavaScript 语言采用的是单线程模型，也就是说，所有任务只能在一个线程上完成，一次只能做一件事。前面的任务没做完，后面的任务只能等着。随着电脑计算能力的增强，尤其是多核 CPU 的出现，单线程带来很大的不便，无法充分发挥计算机的计算能力。
> Web Worker 的作用，就是为 JavaScript 创造多线程环境，允许主线程创建 Worker 线程，将一些任务分配给后者运行。在主线程运行的同时，Worker 线程在后台运行，两者互不干扰。等到 Worker 线程完成计算任务，再把结果返回给主线程。这样的好处是，一些计算密集型或高延迟的任务，被 Worker 线程负担了，主线程（通常负责 UI 交互）就会很流畅，不会被阻塞或拖慢。

### 基本用法

```js
    const worker = new Worker(jsUrl[, { name: 'yourWorkerName' }])
```

<!-- More -->

### Tips

* 分配给 `Worker` 线程运行的脚本文件，必须与主线程的脚本文件同源。
* `Worker` 线程所在的全局对象，与主线程不一样，不能使用 `document`、`window`、`parent`、`alert()`、`confirm()`，但是可以使用 `console`，并且使用 `XMLHttpRequest` 发起 `AJAX` 请求。
* 通过 `importScript` 加载进来的脚本文件里，同样不能使用 `alert` 等属性。加载多个脚本文件时，用逗号隔开。
* `Worker` 线程无法读取本地文件，即不能打开本机的文件系统（`file://`），它所加载的脚本，必须来自网络。

### 主线程 API

```js
    Worker.onerror(): 指定 error 事件的监听函数
    Worker.onmessage(): 指定 message 事件的监听函数
    Worker.onmessageerror(): 指定 messageerror 事件的监听函数
    Worker.postMessage(): 向 Worker 线程发送消息
    Worker.terminate(): 终止 Worker 线程
```

### Worker线程 API

```js
    self.name: Worker 的名字。该属性只读，由构造函数指定
    self.onmessage: 指定 message 事件的监听函数
    self.onmessageerror: 指定 messageerror 事件的监听函数。发送的数据无法序列化成字符串时，会触发这个事件
    self.close(): 关闭 Worker 线程
    self.postMessage(): 向产生这个 Worker 线程发送消息
    self.importScripts(): 加载 JS 脚本
```

### 示例

#### worker.html

```js
    <input type="text" class="text">
    <input type="button" class="button" value="发送">

    const text = document.querySelector('.text')
    const button = document.querySelector('.button')

    const worker = new Worker('./worker.js', { name: 'myworker' })
    worker.onmessage = function(event) {
        console.log('主线程收到来自myworker线程传递的消息：', event.data)
        console.log('------------')
        worker.terminate()
        console.log('主线程终止myworker线程')
    }
    // 当向myworker传递的消息无法序列化为字符串时 触发
    worker.onmessageerror = function(event) {
        // ...
    }
    worker.onerror = function() {
        // ...
    }

    button.addEventListener('click', () => {
        console.log('主线程向myworker线程发送消息：', text.value)
        worker.postMessage(text.value)
    })
```

#### worker.js

```js

    // 均输出 myworker
    console.log(self.name, this.name, name)

    importScripts('./test-importScript.js')

    this.onmessage = function(event) {
        console.log('myworker线程收到主线程传递的消息：', event.data)

        this.postMessage('myworker线程已收到主线程传递的消息')

        this.close()
        console.log('---------')
        console.log('myworker线程已主动断开与主线程的连接，不能再收到传递的消息')
    }

    // 当向主线程传递的消息无法序列化为字符串时 触发
    this.onmessageerror = function() {
        // ...
    }
```

#### test-importScript.js

```js
    // myworker
    console.log(this.name)
```

### 参考

[Web Worker 使用教程 - 阮一峰](http://www.ruanyifeng.com/blog/2018/07/web-worker.html)
