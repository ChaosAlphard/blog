---
title: CSS蛇形边框
date: 2019-08-27 02:27:56
tags: [HTML,CSS]
categories: [CSS]
---

# 成果展示
<iframe src="https://erina.gitee.io/ilirus/CSS%E8%9B%87%E5%BD%A2%E8%BE%B9%E6%A1%86/04.html" frameborder="0" width="200" height="100"></iframe>

> [浏览器支持](https://caniuse.com/#search=clip-path):
> - Chrome 55+
> - Firefox 54+

# 主要思路
> 使用`伪元素`实现一个大于目标元素的背景板，  
> 再使用`clip-path`将其裁剪到只剩上/下/左/右一个边,  
> 再使用`animation`实现动画效果。

# 实现过程
写一个普通的`button`
```html
<!DOCTYPE html>
<html>
<head>
  <meta charset="UTF-8">
  <style>
  .content {
    width: 180px;
    height: 64px;
    margin: 0;
    padding: 0;
    border: none;
    background: transparent;
    position: relative;
    font-size: 24px;
  }
  </style>
</head>
<body>
  <button class="content">我是内容</button>
</body>
</html>
```

加上`::before`
```css
.content::before {
  content: '';
  position: absolute;
  top: -2px; bottom: -2px;
  left: -2px; right: -2px;
  background: olivedrab;
  border: 2px solid olive;
  z-index: -1;
}
```
<iframe src="https://erina.gitee.io/ilirus/CSS%E8%9B%87%E5%BD%A2%E8%BE%B9%E6%A1%86/01.html" frameborder="0" width="200" height="100"></iframe>

使用`clip-path`裁剪
```css
.content::before {
  /* 上 */
  clip-path: inset(0 0 calc(100% - 2px) 0);
  /* 右 */
  clip-path: inset(0 0 0 calc(100% - 2px));
  /* 下 */
  clip-path: inset(calc(100% - 2px) 0 0 0);
  /* 左 */
  clip-path: inset(0 calc(100% - 2px) 0 0);
}
```
<iframe src="https://erina.gitee.io/ilirus/CSS%E8%9B%87%E5%BD%A2%E8%BE%B9%E6%A1%86/02.html" frameborder="0" width="200" height="300"></iframe>

<!-- more -->

使用`animation`实现动画效果
```css
.content::before {
  content: '';
  position: absolute;
  top: -2px; bottom: -2px;
  left: -2px; right: -2px;
  background: olivedrab;
  border: 2px solid olive;
  z-index: -1;
  animation: snake 10s linear infinite;
}

@keyframes snake {
  0%, 100% {
    clip-path: inset(0 0 calc(100% - 2px) 0);
  }
  25%{
    clip-path: inset(0 0 0 calc(100% - 2px));
  }
  50%{
    clip-path: inset(calc(100% - 2px) 0 0 0);
  }
  75%{
    clip-path: inset(0 calc(100% - 2px) 0 0);
  }
}
```
<iframe src="https://erina.gitee.io/ilirus/CSS%E8%9B%87%E5%BD%A2%E8%BE%B9%E6%A1%86/03.html" frameborder="0" width="200" height="100"></iframe>


完善CSS代码
```css
.content {
  width: 180px;
  height: 64px;
  margin: 0;
  padding: 0;
  border: none;
  background: transparent;
  position: relative;
  font-size: 24px;
}
.content::before, .content::after {
  content: '';
  position: absolute;
  top: -2px; bottom: -2px;
  left: -2px; right: -2px;
}
.content::before {
  border: 2px solid olive;
  animation: snake 4s linear infinite;
}
.content::after {
  border: 2px solid plum;
  animation: snake 4s linear infinite;
  animation-delay: -2s;
}

@keyframes snake {
  0%, 100% {
    clip-path: inset(0 0 calc(100% - 2px) 0);
  }
  25%{
    clip-path: inset(0 0 0 calc(100% - 2px));
  }
  50%{
    clip-path: inset(calc(100% - 2px) 0 0 0);
  }
  75%{
    clip-path: inset(0 calc(100% - 2px) 0 0);
  }
}
```
<iframe src="https://erina.gitee.io/ilirus/CSS%E8%9B%87%E5%BD%A2%E8%BE%B9%E6%A1%86/04.html" frameborder="0" width="200" height="100"></iframe>