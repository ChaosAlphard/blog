---
title: CSS蛇形边框
date: 2019-08-27 02:27:56
tags: [HTML,CSS]
categories: [编程]
---

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
  z-index: 0
}
.result::before, .result::after {
  content: '';
  position: absolute;
  top: -2px; bottom: -2px;
  left: -2px; right: -2px;
}
.result::before {
  border: 2px solid olive;
  animation: snake 4s linear infinite;
}
.result::after {
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
</style>

# 成果展示

<button class="content result">我是内容</button>

如果显示出现问题则说明你的浏览器不支持[clip-path](https://caniuse.com/#search=clip-path)

> [浏览器支持](https://caniuse.com/#search=clip-path):
> - Chrome 55+
> - Firefox 54+

# 主要思路
> 使用`伪元素`实现一个大于目标元素的背景板.  
> 再使用`clip-path`将其裁剪到只剩上/下/左/右一个边.  
> 再使用`animation`实现动画效果.

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

<style>
.st::before {
  content: '';
  position: absolute;
  top: -2px; bottom: -2px;
  left: -2px; right: -2px;
  background: olivedrab;
  border: 2px solid olive;
  z-index: -1;
}
</style>
<button class="content st">我是内容</button>

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
<style>
  .block {
    display: block;
  }
  .st21::before {
    clip-path: inset(0 0 calc(100% - 2px) 0);
  }
  .st22::before {
    clip-path: inset(0 0 0 calc(100% - 2px));
  }
  .st23::before {
    clip-path: inset(0 calc(100% - 2px) 0 0);
  }
  .st24::before {
    clip-path: inset(calc(100% - 2px) 0 0 0);
  }
</style>
<button class="content st block st21">我是内容</button>
<button class="content st block st22">我是内容</button>
<button class="content st block st23">我是内容</button>
<button class="content st block st24">我是内容</button>

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
<style>
.st1::before {
  animation: snake 10s linear infinite;
}
</style>
<button class="content st st1">我是内容</button>

去掉`伪元素`的背景

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
.content::before {
  content: '';
  position: absolute;
  top: -2px; bottom: -2px;
  left: -2px; right: -2px;
}
.content::before {
  border: 2px solid olive;
  animation: snake 4s linear infinite;
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

<style>
.content3 {
  width: 180px;
  height: 64px;
  margin: 0;
  padding: 0;
  border: none;
  background: transparent;
  position: relative;
  font-size: 24px;
}
.content3::before {
  content: '';
  position: absolute;
  top: -2px; bottom: -2px;
  left: -2px; right: -2px;
}
.content3::before {
  border: 2px solid olive;
  animation: snake 4s linear infinite;
}
</style>
<button class="content3">我是内容</button>

再使用`::after` 伪元素添加一个新的边框，再使用`animation-delay` 错开动画时间

<button class="content result">我是内容</button>
