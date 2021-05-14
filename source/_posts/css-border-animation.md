---
title: css-border-animation
date: 2021-01-19 10:11:11
tags: [css, border]
---

<div class="css-border1">Hover Me</div>

```html
<div class="css-border1">Hover Me</div>
```

<!-- more -->

```yaml
.css-border1 {
    width: 100px; 
    height: 100px;
    line-height: 100px;
    text-align: center;
    cursor: pointer;
    background: linear-gradient(to right, currentColor 50%, transparent 50%) repeat-x,
                linear-gradient(to right, currentColor 50%, transparent 50%) repeat-x,
                linear-gradient(to bottom, currentColor 50%, transparent 50%) repeat-y,
                linear-gradient(to bottom, currentColor 50%, transparent 50%) repeat-y;
    background-size: 5px 1px, 5px 1px, 1px 5px, 1px 5px;
    background-position: 0 0, 0 100%, 0 0, 100% 0;
}
.css-border1:hover {
    animation: animation1 .1s linear infinite;
}
@keyframes animation1 {
    100% {
        background-position: 5px 0, -5px 100%, 0 -5px, 100% 5px;
    }
}
```

利用渐变+伪元素画出来四块背景图形
<div class="border-wrapper2"><div class="css-border2"></div></div>

```html
<div class="border-wrapper2"><div class="css-border2"></div></div>
```
```yaml
.border-wrapper2 {
    width: 100px;
    height: 100px;
    overflow: hidden;
    position: relative;
    border-radius: 10px;
}
.css-border2 {
    width: 100px;
    height: 100px;
    position: relative;
}
.css-border2:after {
    content: '';
    position: absolute;
    top: -50%;
    left: -50%;
    width: 200%;
    height: 200%;
    background-image: linear-gradient(#399953, #399953),
                      linear-gradient(#fbb300, #fbb300),
                      linear-gradient(#d53e33, #d53e33),
                      linear-gradient(#377af5, #377af5);
    background-size: 50% 50%;
    background-position: 0 0, 100% 0, 100% 100%, 0 100%;
    background-repeat: no-repeat;
}
```

在上图基础上，加上遮罩盖掉多余的部分，并加上旋转 [codepen示例](https://codepen.io/Chokcoco/pen/YzGdEMZ)
<div class="border-wrapper3"><div class="css-border3"></div></div>

```html
<div class="border-wrapper3"><div class="css-border3"></div></div>
```
```yaml
.border-wrapper3 {
    width: 100px;
    height: 100px;
    overflow: hidden;
    position: relative;
    border-radius: 10px;
}
.border-wrapper3:before {
    content: '';
    position: absolute;
    z-index: 10;
    top: 2px; bottom: 2px;
    left: 2px; right: 2px;
    background: #fff;
    border-radius: 10px;
}
.css-border3 {
    width: 100px;
    height: 100px;
    animation: animation2 2s linear infinite;
    position: relative;
}
.css-border3:after {
    content: '';
    position: absolute;
    top: -50%;
    left: -50%;
    width: 200%;
    height: 200%;
    background-image: linear-gradient(#399953, #399953),
                      linear-gradient(#fbb300, #fbb300),
                      linear-gradient(#d53e33, #d53e33),
                      linear-gradient(#377af5, #377af5);
    background-size: 50% 50%;
    background-position: 0 0, 100% 0, 100% 100%, 0 100%;
    background-repeat: no-repeat;
}
@keyframes animation2 {
    100% {
        transform: rotate(1turn);
    }
}
```

conic-gradient 角向(圆锥)渐变 (edge≥79支持) [codepen示例](https://codepen.io/Chokcoco/pen/dypaobm)
<div class="css-border4"></div>

conic-gradient 饼图 (怎么感觉有种opacity的效果😶)
<div class="css-pie" style="width: 200px;height: 200px; border-radius: 50%; background: conic-gradient(pink 0, pink 30%, yellow 30%, yellow 70%, oldlace 70%, oldlace 100%);"></div>

重复圆锥渐变绘制抽奖圆盘 [codepen示例](https://codepen.io/jianxiujiucan/pen/bGddbez)
<div class="css-lottery" style="width: 200px; height: 200px; border-radius: 50%; background: repeating-conic-gradient(#ff8586 0 45deg, #ff9e9d 45deg 90deg)"></div>

圆锥渐变绘制信封效果 [codepen示例](https://codepen.io/jianxiujiucan/pen/gOppOdP)