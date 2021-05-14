---
title: 前端优化之Pre相关术语
date: 2020-09-09 14:00:56
tags: 前端优化
---

### DNS prefetch

用于告知浏览器预先获取指定的域名的DNS，以便在使用时可以减少DNS查找。

```html
<link rel="dns-prefetch" href="//the-domain-to-be-prefetched.com" />
```

<!-- more -->

### preconnect 预连接

预连接。和DNS Prefetch类似，但是，更进一步。它不仅要求浏览器预解析指定域名的DNS，还需要预先与服务器握手以及TLS协商（如果使用了HTTPS）。

```html
<link rel="preconnect" href="//the-domain-to-be-preconnect.com" crossorigin="anonymous" />
```

### preload 预加载

> link 元素的 rel 属性的属性值 preload 能够让你在你的HTML页面中 head 元素内部书写一些声明式的资源获取请求，可以指明哪些资源是在页面加载完成后即刻需要的。对于这种即刻需要的资源，你可能希望在页面加载的生命周期的早期阶段就开始获取，在浏览器的主渲染机制介入前就进行预加载。这一机制使得资源可以更早的得到加载并可用，且更不易阻塞页面的初步渲染，进而提升性能。

preload 的资源会被优先加载，但不会阻塞doument的渲染以及页面的onload事件。一般用于对页面很重要的资源上，比如 `字体文件`。如果预加载进来的资源在3s内没有使用，浏览器会在控制台打印警告信息。

preload有许多好处：

- 更精确地优化资源加载优先级。
- 匹配未来的加载需求，在适当的情况下，重复利用同一资源。
- 为资源应用正确的内容安全策略。
- 为资源设置正确的 Accept 请求头。

预 prefetch 的区别

- Prefetch的优先级很低，低到浏览器可以不加载
- Preload的优先级很高，浏览器一定要加载
- Preload加载的资源用于当前页面中
- Prefetch加载的资源用于将来某个页面中（这意味着它可能压根不会被用到）

#### 指定资源类型 as

指定被预加载资源的类型。as属性值为css/script时，优先级很高。

#### 跨域获取 crossOrigin

如需加载跨域的资源列表，则需要正确设置CORS，接着便可以在 link 元素中设置好crossorigin属性即可。(由于历史原因，加载字体时，无论有没有跨域，都需要添加crossOrigin)

```html
<link rel="preload" as="font" type="font/woff2" href="path/to/font" crossOrigin>
```

#### 包含媒体 media

link 元素有一个很棒的特性是它们能够接受一个media属性。它们可以接受媒体类型或有效的媒体查询作为属性值，这将令你能够使用响应式的预加载！

```html
<link rel="preload" href="bg-image-narrow.png" as="image" media="(max-width: 600px)">
<link rel="preload" href="bg-image-wide.png" as="image" media="(min-width: 601px)">
```

```js
var mediaQueryList = window.matchMedia("(max-width: 600px)");
var header = document.querySelector('header');

if(mediaQueryList.matches) {
    header.style.backgroundImage = 'url(bg-image-narrow.png)';
} else {
    header.style.backgroundImage = 'url(bg-image-wide.png)';
}
```

#### 脚本化预加载

预加载

```js
var preloadLink = document.createElement("link");
preloadLink.href = "myscript.js";
preloadLink.rel = "preload";
preloadLink.as = "script";
document.head.appendChild(preloadLink);
```

执行

```js
var preloadedScript = document.createElement("script");
preloadedScript.src = "myscript.js";
document.body.appendChild(preloadedScript);
```

### prefetch 预获取

关键字 prefetch 作为元素 link 的属性 rel 的值，是为了提示浏览器，用户未来的浏览有可能需要加载目标资源，所以浏览器有可能通过事先获取和缓存对应资源，优化用户体验。

### prerender 预渲染

> 与Prefetch类似，浏览器会下载整个资源，但是，Prefetch不允许浏览器对资源做预处理和执行，而Prerender则会告知浏览器，下载资源（HTML）并解析/执行它，包括被解析资源的子资源，这意味着会下载该HTML中的图片、样式、脚本等等。Prerender没有as属性，它下载的是HTML

```html
<link rel="prerender" href="//the-domain-to-be-preconnect.com" />
```

### subresource

指定预加载资源拥有加载的最高优先级

### script defer

```js
<script src="path/to/script.js" defer></script>
```

加载后续文档元素的过程与 script.js 的加载并行进行（异步），但是 script.js 的执行要等到所有文档元素加载和解析之后，在 DOMContentLoaded 之前执行。

### script async

```js
<script src="path/to/script.js" async></script>
```

加载后续文档元素的过程与 script.js 的加载以及执行都并行进行（异步）。

### script

```js
<script src="path/to/script.js"></script>
```

浏览器会立即加载并执行 script.js. `立即` 指的是在渲染script标签之后（下）的文档元素之前，不等待标签之后的文档元素载入，读到了就开始加载并执行。

附三种情形下的图示（ [图片来源](https://segmentfault.com/q/1010000000640869)）

![差异对比图](/assets/img/script-render.png)

### ES Module

[详情参考](https://blog.shenfq.com/2019/%E5%89%8D%E7%AB%AF%E6%A8%A1%E5%9D%97%E5%8C%96%E7%9A%84%E4%BB%8A%E7%94%9F/)

### 资料

- [MDN](https://developer.mozilla.org/zh-CN/docs/Web/HTML/Preloading_content)
- [前端模块化的今生](https://blog.shenfq.com/2019/%E5%89%8D%E7%AB%AF%E6%A8%A1%E5%9D%97%E5%8C%96%E7%9A%84%E4%BB%8A%E7%94%9F/)