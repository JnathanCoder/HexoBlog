---
title: vue自定义生命周期钩子
date: 2020-08-25 17:49:19
tags: [vue]
---

### 先摘抄原作者文章中引用的话吧~

> 葡萄美酒夜光杯，欲饮琵琶产品催。 客户现场君莫笑，古来埋坑几人回？

> 不要吹灭你的灵感和你的想象力; 不要成为你的模型的奴隶。 ——文森特・梵高

### 然后也不知道说啥，来点示例代码吧

<!-- more -->

#### utils/emitter.js

```js

import Vue from 'vue'

/**
 * 通知组件及其子组件页面可见状态变更
 * @param {String} lifeCycleName 生命周期钩子名
 * @param {vmInstance} vm 实例
 */
const notifyVisibilityChange = (lifeCycleName, vm) => {

    // 生命周期函数会存在$options中，通过$options[lifeCycleName]获取生命周期
    const lifeCycles = vm.$options[lifeCycleName]
    // 因为使用了created的合并策略，所以是一个数组
    if (lifeCycles && lifeCycles.length) {
        // 遍历 lifeCycleName对应的生命周期函数列表，依次执行
        lifeCycles.forEach(lifecycle => {
            process.env.NODE_ENV !== 'production' && console.info('自定义' + lifeCycleName + '生命周期钩子已触发，组件name：' + vm.$options.name)
            lifecycle.call(vm)
        })
    }
    // 遍历所有的子组件，然后依次递归执行
    if (vm.$children && vm.$children.length) {
        vm.$children.forEach(child => {
            notifyVisibilityChange(lifeCycleName, child)
        })
    }
}

const CustomHooks = {
    /**
     * 安装注册
     */
    Attach() {
        // Vue的合并策略
        const optionMergeStrategies = Vue.config.optionMergeStrategies

        // 定义了两个生命周期函数 pageVisible, pageHidden
        // 这个相当于指定 pageVisible, pageHidden 的合并策略与 created的相同（使用其他生命周期函数都一样）
        optionMergeStrategies.pageVisible = optionMergeStrategies.created
        optionMergeStrategies.pageHidden = optionMergeStrategies.created
    },
    /**
     * 绑定监听
     * @param {vmInstance} rootVm 根实例
     */
    Observer(rootVm) {
        window.addEventListener('visibilitychange', () => {
            // 判断调用哪个生命周期函数
            let lifeCycleName = undefined
            if (document.visibilityState === 'hidden') {
                lifeCycleName = 'pageHidden'
            } else if (document.visibilityState === 'visible') {
                lifeCycleName = 'pageVisible'
            }
            if (lifeCycleName) {
                // 通过所有组件生命周期发生变化了
                notifyVisibilityChange(lifeCycleName, rootVm)
            }
        })
    }
}

export default CustomHooks
```

#### main.js

```js
import CustomHooks from '@/utils/custom-emitter'

// 实例化之前进行初始化
CustomHooks.Attach()

/* eslint-disable no-new */
const rootVm = new Vue({
    el: '#app',
    router,
    components: { App },
    template: '<App/>'
})

// 实例化之后绑定监听
CustomHooks.Observer(rootVm)
```

#### 使用 demo.vue

```js
export default {
    name: "demo",
    data() {
        return {}
    },
    created() {},
    pageVisible() {
        // TODO 页面可见逻辑 如开启已暂停的功能
    },
    pageHidden() {
        // TODO 页面不可见逻辑 如暂停某些花销很大的动画、ajax轮询
    }
}
```

### 参考文章

[前方高能，这是最新的一波Vue实战技巧](https://juejin.im/post/6844904200598454286#heading-14)