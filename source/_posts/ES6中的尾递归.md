---
title: ES6中的尾递归
date: 2019-06-01 15:29:46
tags: [JavaScript, ES6+]
categories: [JavaScript,ECMAScript 6+]
---

假设要用`JavaScript`实现一个阶乘函数，很容易想到用递归实现:
```javascript
function factorial(n) {
  if(n === 1) {
    return 1
  }
  return n * factorial(n-1)
}

console.log(factorial(4))   // 24
```

在上述代码中，由于最后一步返回了一个表达式(`n * factorial(n-1)`)，所以会保留`n`这个变量和函数`factorial(n-1)`的调用位置等信息，从而形成一层层的调用帧。

递归十分消耗内存，因为要同时保存非常多个调用记录，容易出现"栈溢出"错误`(Stack Overflow)`

<!-- more -->

# 尾调用
尾调用是指的一个函数的最后一个动作是返回另一个函数的调用
```javascript
function foo(n) {
  return meh(n)
}

// 并不一定要在函数尾部，只要是最后一步操作即可
// 以下meh 和nco 函数均属于尾调用
function foo(n) {
  if(n>0) {
    return meh(n)
  }
  return nco(n)
}
```

<!-- moer -->

不属于尾调用的情况
```javascript
// 函数调用后还有赋值操作，所以不属于尾调用，即使语义一样
function foo(n) {
  let tmp = meh(n)
  return tmp
}

// 函数调用后还有相加操作
function foo(n) {
  return meh(n) + 1
}

// 没有返回函数调用
function foo(n) {
  meh(n)
}
```

# 尾调用优化
>　　函数调用会在内存形成一个"调用记录"，又称"调用帧" (call frame)，保存调用位置和内部变量等信息。如果在函数A的内部调用函数B，那么在A的调用帧上方，还会形成一个B的调用帧。等到B运行结束，将结果返回到A，B的调用帧才会消失。如果函数B内部还调用函数C，那就还有一个C的调用帧，以此类推。所有的调用帧，就形成一个"调用栈" (call stack)。  
>　　尾调用由于是函数的最后一步操作，所以不需要保留外层函数的调用帧，因为调用位置、内部变量等信息都不会再用到了，只要直接用内层函数的调用帧，取代外层函数的调用帧就可以了。

举例:
```javascript
function getItem(item) {
  return item
}

function foo() {
  let a=1
  let b=2
  return getItem(a+b)
}
foo()
```

上述代码中，如果函数`getItem`不是尾调用，函数`foo`就要保存内部变量`m`和`n`的值，以及函数`getItem`的调用位置等信息。  
但如果调用`getItem`后，函数`foo`就结束了，所以执行到最后一步，可以删除`foo`的调用帧，只保留`getItem(3)`的调用帧。

>"尾调用优化" (Tail call optimization)，即只保留内层函数的调用帧。  
> **PS: 只有不再用到外层函数的内部变量，内层函数的调用帧才会取代外层函数的调用帧，否则就无法进行"尾调用优化"。**

# 尾递归
函数调用自身，称为递归；如果尾调用自身，就称为尾递归。

>对于尾递归来说，由于只存在一个调用帧，所以能有效的避免"栈溢出"错误的发生。

<!-- <s>"尾调用优化"对递归操作意义重大，所以ES6也明确规定了，所有`ECMAScript`的实现，都必须部署"尾调用优化"。</s> -->

改写之前的代码:
```javascript
function factorial(n,total) {
  "use strict"
  total = total || 1
  if(n <= 1) return total
  return factorial(n-1, n*total)
}
```

**ES6的尾调用优化只在严格模式下开启，正常模式是无效的**

在正常模式下，函数内部有两个变量，可以跟踪函数的调用栈。
- arguments: 返回调用时函数的参数。
- caller: 返回调用当前函数的那个函数。

尾调用优化发生时，函数的调用栈会改写，因此上面两个变量就会失真。严格模式禁用这两个变量，所以尾调用模式仅在严格模式下生效。

参考:  
> https://github.com/tc39/proposal-ptc-syntax/blob/master/README.md

**补充(2019.06.02): 出于某些因素的考虑，目前V8引擎中的尾递归优化已经被去掉了**

大概原因可以看这篇博客:  
> https://imweb.io/topic/5a244260a192c3b460fce275

# 尾递归优化的实现
正常模式下如何实现尾递归优化呢?

```javascript
function sum(x,y) {
  if (y > 0) {
    return sum(x + 1, y - 1);
  } else {
    return x;
  }
}
sum(1,100000)
```


尾递归之所以需要优化，原因是调用栈太多，造成溢出，那么只要减少调用栈，就不会溢出。

```javascript
function tco(f) {
  var value;
  var active = false;
  var accumulated = [];

  return function accumulator() {
    accumulated.push(arguments);
    if (!active) {
      active = true;
      while (accumulated.length) {
        value = f.apply(this, accumulated.shift());
      }
      active = false;
      return value;
    }
  };
}

var sum = tco(function sum(x,y) {
  if (y > 0) {
    return sum(x + 1, y - 1);
  } else {
    return x;
  }
})

sum(1,100000)
```