---
title: CSS实现毛玻璃模糊效果
date: 2018-05-28 02:10:10
tags: [HTML,CSS]
categories: [CSS]
---

# 效果展示
![](https://s2.ax1x.com/2019/06/16/V7AoXd.png)

# 效果实现
一个div，将文字放进去
```html
<body>
    <div class="box">Welcome to Ilirus' blog</div>
</body>
```

美化样式
```css
body {
    --bg:url("image.jpg");
    background: var(--bg) fixed top center / cover;
}
.box{
    background: hsla(0,0%,100%,0.2);
    color: aliceblue;
    font-size: 38px;
    width: 12em;
    height: 3em;
    display: flex;
    align-items: center;
    justify-content: center;
    border-radius: 0.6em;
    box-shadow: 0px 0px 3px 3px hsla(0,0%,0%,0.4);
    text-shadow: 0px 0px 8px #FFF;
}
```

<!-- more -->

大致效果应该是这样的  
![](https://s2.ax1x.com/2019/06/16/V7A7nA.png)

**使用伪元素实现模糊效果**

为div创建一个伪元素
```css
.box::before{
    content: '';
}
```

设置定位和宽高
```css
.box::before{
    position: absolute;
    width: inherit;
    height: inherit;
}
```

添加背景颜色，以便观察调试
```css
.box::before{
    background: hsla(0,100%,83%,0.4);
}
```

现在效果应该是这样  
![](https://s2.ax1x.com/2019/06/16/V7Ab7t.png)

添加模糊效果和border-radius
```css
.box::before{
    border-radius: 0.6em;
    filter: blur(5px);
}
```

![](https://s2.ax1x.com/2019/06/16/V7ALAP.png)

伪元素把文本挡住了，给它设置z-index
```css
.box::before{
    z-index: -1;
}
```

![](https://s2.ax1x.com/2019/06/16/V7AH0I.png)

现在将背景换成图片就可以了
```css
.box::before{
    background: var(--bg) fixed top center / cover;
}
```

![](https://s2.ax1x.com/2019/06/16/V7AoXd.png)

CSS代码
```css
body {
    --bg:url("image.jpg");
    background: var(--bg) fixed top center / cover;
}
.box{
    background: hsla(0, 0%, 100%, 0.2);
    color: aliceblue;
    font-size: 38px;
    width: 12em;
    height: 3em;
    display: flex;
    align-items: center;
    justify-content: center;
    border-radius: 0.6em;
    box-shadow: 0px 0px 3px 3px hsla(0, 0%, 0%, 0.4);
    text-shadow: 0px 0px 8px #FFF;
}
.box::before{
    content: '';
    border-radius: 0.6em;
    position: absolute;
    width: inherit;
    height: inherit;
    background: var(--bg) fixed top center / cover;
    filter: blur(10px);
    z-index: -1;
}
```