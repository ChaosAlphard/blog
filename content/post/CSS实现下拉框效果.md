---
title: CSS实现下拉框效果
date: 2018-06-12 21:20:50
tags: [HTML,CSS]
categories: [编程,前端]
---

# 效果展示
![](https://s2.ax1x.com/2019/06/16/V7E1N6.gif)

# 效果实现
## 方法一
### 准备工作
首先把导航栏效果做出来

<!-- more -->

HTML
```html
<div class="nav">
    <div class="top">
        <div class="title">Title1</div>
        <div class="list">
            <a href="">xxxxxx</a>
            <a href="">xxxxxx</a>
            <a href="">xxxxxx</a>
        </div>
    </div>
    <div class="top">
        <div class="title">Title2</div>
        <div class="list">
            <a href="">xxxxxx</a>
            <a href="">xxxxxx</a>
            <a href="">xxxxxx</a>
            <a href="">xxxxxx</a>
            <a href="">xxxxxx</a>
        </div>
    </div>
    <div class="top">
        <div class="title">Title3</div>
        <div class="list">
            <a href="">xxxxxx</a>
            <a href="">xxxxxx</a>
            <a href="">xxxxxx</a>
            <a href="">xxxxxx</a>
            <a href="">xxxxxx</a>
            <a href="">xxxxxx</a>
            <a href="">xxxxxx</a>
            <a href="">xxxxxx</a>
        </div>
    </div>
</div>
```

CSS
```css
.nav{
    width: 1200px;
    height: 72px;
    display: flex;
    background-color: #DDD;
    justify-content: center;
}
.top{
    margin: 0 20px 0 20px;
    background-color: #EEE;
    width: 10%;
    height: 100%;
}
.title{
    display: flex;
    height: 100%;
    font-family: "微软雅黑";
    font-size: 36px;
    justify-content: center;
    align-items: center;
}
```

再给下拉框设置样式
```css
.list{
    width: 100%;
    background: #EEE;
}
.list>a{
    display: flex;
    justify-content: center;
    padding: 10px 0 10px 0;
    font-family: "微软雅黑";
    font-size: 24px;
    text-decoration: none;
    color: #000;
}
```
现在效果应该是这样的  
![](https://s2.ax1x.com/2019/06/16/V7EG9O.png)

### 控制下拉框的显示与隐藏
隐藏下拉框
```css
.list{
    opacity: 0;
    transform: scaleY(0);
}
```

显示下拉框
```css
.top:hover>.list{
    opacity: 1;
    transform: scaleY(1);
}
```

### 添加动画效果
下拉效果
```css
.list{
    opacity: 0;
    transform: scaleY(0);
    transform-origin: top center;
    transition: opacity 0.5s ease-out,transform 0.5s;
}
.top:hover>.list{
    opacity: 1;
    transform: scaleY(1);
}
```
![](https://s2.ax1x.com/2019/06/16/V7EJ3D.gif)

淡出淡入效果
```css
.list{
    opacity: 0;
    transform: scaleY(0);
    transform-origin: top center;
    transition: opacity 0.5s ease-out,transform 0s 0.5s;
}
.top:hover>.list{
    opacity: 1;
    transform: scaleY(1);
    transition: opacity 0.5s ease-out,transform 0s;
}
```
![](https://s2.ax1x.com/2019/06/16/V7EtjH.gif)

## 方法二
### 准备工作
HTML
```html
<div class="nav">
    <div class="top">
        <div class="title t1">
            <a href="" class="first">Title1</a>
            <a href="">xxxxxx</a>
            <a href="">xxxxxx</a>
            <a href="">xxxxxx</a>
        </div>
    </div>
    <div class="top">
        <div class="title t2">
            <a href="" class="first">Title2</a>
            <a href="">xxxxxx</a>
            <a href="">xxxxxx</a>
            <a href="">xxxxxx</a>
            <a href="">xxxxxx</a>
            <a href="">xxxxxx</a>
        </div>
    </div>
    <div class="top">
        <div class="title t3">
            <a href="" class="first">Title3</a>
            <a href="">xxxxxx</a>
            <a href="">xxxxxx</a>
            <a href="">xxxxxx</a>
            <a href="">xxxxxx</a>
            <a href="">xxxxxx</a>
            <a href="">xxxxxx</a>
            <a href="">xxxxxx</a>
            <a href="">xxxxxx</a>
        </div>
    </div>
</div>
```

CSS
```css
.nav{
    width: 100vw;
    height: 72px;
    display: flex;
    margin-top: 200px;
    background-color: #DDD;
    justify-content: center; 
}
.top{
    margin: 0 20px 0 20px;
    background-color: #EEE;
    width: 10%;
    height: 100%;
    position: relative;
}
.title>a{
    display: block;
    font-family: "微软雅黑";
    text-decoration: none;
    color: #000;
    text-align: center;
    background: #EEE;
}
.title>.first{
    height: 72px;
    line-height: 72px;
    font-size: 36px;
}
.title>a:not(.first){
    height: 52px;
    line-height: 52px;
    font-size: 24px;
}
```
![](https://s2.ax1x.com/2019/06/16/V7EG9O.png)

### 无动画效果的显示与隐藏
隐藏下拉框
```css
.title{
    height: 72px;
    overflow: hidden;
}
```

显示下拉框
```css
.title:hover{
    overflow: visible;
}
```

### 有动画效果的显示与隐藏
隐藏下拉框
```css
.title{
    height: 72px;
    overflow: hidden;
    transition: height 0.5s ease-out;
}
```

显示下拉框
```css
.title:hover{
/* 52px为下拉框项的高度, 72px为下拉框Title项的高度 */
    height: calc(var(--n) * 52px + 72px);
}
/* n为下拉框项的数量 */
.t1{
    --n:3;
}
.t2{
    --n:5;
}
.t3{
    --n:8;
}
```
效果  
![](https://s2.ax1x.com/2019/06/16/V7EYge.gif)