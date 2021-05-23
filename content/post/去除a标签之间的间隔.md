---
title: 去除a标签之间的间隔
date: 2018-05-24 12:28:42
tags: [HTML,CSS]
categories: [编程,前端]
---

# 为什么a标签之间会有间隔
在网页的编写过程中经常会发现相邻的a标签之间有间隔
```html
<div>
    <a href="">1</a>
    <a href="">2</a>
    <a href="">3</a>
    <a href="">下一页</a>
</div>
```

![](https://s2.ax1x.com/2019/06/16/V7A3lQ.jpg)

<!-- more -->

这其实是网页元素之间的留空导致的，那么最简单的解决方式就是a标签之间不留空格
```html
<div>
    <a href="">1</a><a href="">2</a><a href="">3</a><a href="">下一页</a>
</div>
```

但是考虑到代码的可读性，这样写肯定是不行的，所以我们可以用注释来解决
```html
<div>
    <a href="">1</a><!--
 --><a href="">2</a><!--
 --><a href="">3</a><!--
 --><a href="">下一页</a>
</div>
```

但这样也不是长久之策，如果a标签数量较多的话就会很麻烦，所以还是得需要CSS来实现

**使CSS来消除a标签之间的间隔**

## 方法一
**给a标签的父级元素设置font-size属性**
```css
div {
    font-size:0px;
}
div>a {
    font-size:36px;
}
```
![](https://s2.ax1x.com/2019/06/16/V7A1Sg.jpg)

缺点：子文本元素**一定要设置font-size**，否则会继承父元素的font-size: 0px 导致不显示。  

下面这张图是我没有给a标签设置font-size参数的情况，  
可以看到，由于我没有给a标签设置font-size数值，a标签继承了父级标签的font-size 大小导致字体小到看不见。  
但是一般情况下通常都会为文本元素设置font-size的大小，所以这个缺点影响不大。  
![](https://s2.ax1x.com/2019/06/16/V7Ac01.jpg)


## 方法二
**给a标签的父级元素设置letter-spacing属性**
```css
div {
/*一般来说-4px即可，但这个数值可以被加大*/
/*数值越大，子元素没有设置letter-spacing时错位效果越严重*/
    letter-spacing:-4px;
}
div>a {
    letter-spacing: 0;
}
```
![](https://s2.ax1x.com/2019/06/16/V7A1Sg.jpg)

缺点：子文本元素**一定要设置letter-spacing: 0**，否则会继承父元素的letter-spacing 导致文字错位。

下面这张图是我没有给a标签设置letter-spacing参数的情况，可以看到，在父级标签letter-spacing参数设为-4px时，即使a标签文本错位，在一定程度上也不影响阅读，所以我比较喜欢用这种写法。  
![](https://s2.ax1x.com/2019/06/16/V7AQfS.jpg)


## 方法三
**父元素使用flex布局**
```css
div{
    display: flex;
/*justify-content可接受的值有[flex-start|center|flex-end]*/
    justify-content: flex-start;
    align-items: center;
}
```
![](https://s2.ax1x.com/2019/06/16/V7A1Sg.jpg)

子元素并不需要特别设置属性，但是会改变布局结构，视情况使用。