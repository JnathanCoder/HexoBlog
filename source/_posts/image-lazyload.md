---
title: Vue自定义指令 image-lazyLoad
date: 2020-08-13 17:37:41
tags: [vue, 自定义指令, lazy]
reward: true
---

主要使用 `IntersectionObserver` 的特性实现，其还能实现譬如 `sticky` 、`上拉加载` 等功能。
这里只记录实现图片懒加载(图片开始出现在视口区域或者即将出现在视口区域，才开始请求图片资源，进行渲染)的自定义指令的实现代码。

<!-- More -->

#### imageLazyLoad.js

```js

// 加载图片
function fetchImage(target, src) {
    const tempImg = new Image()
    tempImg.src = src
    tempImg.onload = () => {
        target.src = src
    }
}

let isIntersecting = false
// 创建一个监视器
let observer = new IntersectionObserver( (entries) => {
    entries.forEach( (entry) => {
        // 已经进入视口 保证只触发一次fetchImage
        if (isIntersecting) return
        if (entry.isIntersecting) {
            isIntersecting = true
            fetchImage(entry.target, entry.target.lazySrc)
        }
    })
}, {
    // 距离视口100px提前触发
    rootMargin: '100px 0px'
})

export default {
    inserted(el, binding) {

        // 默认图片
        el.src = 'http://localhost:8080/static/images/before_load.png'
        // 图片地址绑定在lazy-src属性上，作为中转
        el.lazySrc = binding.value
        // 开始监听
        observer.observe(el)
    },
    unbind() {
        // 解除监听
        observer.disconnect()
    }
}
```

#### 全局注册 main.js

```js
    import Vue from 'vue'
    import imageLazyLoad from './imageLazyLoad'
    Vue.directive('lazyLoad', imageLazyLoad)
```

#### 局部注册 test.vue

```vue
    import imageLazyLoad from './imageLazyLoad'
    export default {
        created() {},
        directives: {
            lazyLoad: imageLazyLoad
        }
    }
```

#### 使用 test.vue

```vue
    <img v-lazy-load="imgSrc" />
```
