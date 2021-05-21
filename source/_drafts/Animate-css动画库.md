---
title: 'Animate.css动画库'
date: 2018-12-12 16:08:20
tags: [CSS,Animate.css]
---

# Animate.css简介
　　Animate.css 是一个有趣，酷炫的，跨浏览器的动画库，你可以将它用于你的项目中。不管是主页，滑动切换，又或者是其它方面，你都可以通过它来制作出惊人的效果。

# 下载与使用
下载[Animate.css](https://daneden.github.io/animate.css/)。  
下载完成后通过`<link rel="stylesheet" type="text/css" href="animate.css">`将其引入。

在HTML元素的class中加入`animated`类和对应的动画类即可使用。  
动画效果可在[这里](https://daneden.github.io/animate.css/)进行预览。

```html
<div class="animated bounce"></div>
```

<!-- more -->

# 事件监听
使用`transitionstart`和`transitionend`来监听过渡的开始与结束  
使用`animationstart`和`animationend`来监听动画的开始与结束。

需要注意的是，如果transition变换的属性有多个，则会多次触发`transitionend`事件

```javascript
element.addEventListener('transitionstart',callback);
element.addEventListener('transitionend',callback);
```