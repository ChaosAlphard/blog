---
title: 'JavaScript中的预编译'
date: 2019-1-14 15:20:50
tags: [JavaScript]
---

# 预编译
假设有:
```javascript
function fn(a){
    console.log(a)

    var a = 123;

    console.log(a)

    function a() {}

    console.log(a)

    var b = function() {}

    console.log(d)

    function d() {}
}

fn(1)
```

那么，`console.log`输出的结果会是什么?

```javascript
function a() {}
123
123
function d() {}
```

`/* 函数的预编译发生在函数执行的前一刻 */`

1. 创建AO对象 - Activation Object (执行期上下文)
```javascript
AO { }
```

2. 找形参和变量声明，将形参和变量名作为AO属性名，值为undefined

`/* 预编译不看if, if块中的变量声明一样要放到AO中 */`
```javascript
AO{
    a: undefined,
    b: undefined
}
```

3. 将实参值和形参统一
```javascript
AO{
    a: 1,
    b: undefined
}
```

4. 找函数体里面的函数声明，然后值赋予函数体
```javascript
AO{
    a: function a() {},
    b: undefined,
    d: function d() {}
}
```

预编译完成，开始执行

# 执行
`/* javascript是解释型语言，读一句，执行一句 */`

```javascript
function fn(a){
    console.log(a)

    var a = 123;

    console.log(a)

    function a() {}

    console.log(a)

    var b = function() {}

    console.log(d)

    function d() {}
}
fn(1)

/* 执行期上下文
AO{
    a: function a() {},
    b: undefined,
    d: function d() {}
}
*/
```

1. 执行第一句: console.log(a)  
从AO对象里面读取a的值，并打印。
```javascript
AO{
    a: function a() {},
    b: undefined,
    d: function d() {}
}
```

2. 执行第二句: var a = 123;  
变量声明在预编译时已经读过了，所以执行器会略过变量声明  
所以，相当于执行`a=123`
```javascript
AO{
    a: 123,
    b: undefined,
    d: function d() {}
}
```

3. 执行第三句: console.log(a)  
从AO对象里面读取a的值，并打印。
```javascript
AO{
    a: 123,
    b: undefined,
    d: function d() {}
}
```

4. 执行第四句: function a() {}  
函数声明在预编译时已经读过了，所以执行器会略过
```javascript
AO{
    a: 123,
    b: undefined,
    d: function d() {}
}
```

5. 执行第五句: console.log(a)  
从AO对象里面读取a的值，并打印。
```javascript
AO{
    a: 123,
    b: undefined,
    d: function d() {}
}
```

6. 执行第六句: var b = function() {}  
变量声明在预编译时已经读过了，所以执行器会略过变量声明  
所以，相当于执行`b = function() {}`
```javascript
AO{
    a: 123,
    b: function() {},
    d: function d() {}
}
```

7. 执行第七句: console.log(d)  
从AO对象里面读取d的值，并打印。
```javascript
AO{
    a: 123,
    b: function() {},
    d: function d() {}
}
```

8. 最后一句: function d() {}  
函数声明在预编译时已经读过了，所以执行器会略过
```javascript
AO{
    a: 123,
    b: function() {},
    d: function d() {}
}
```

`/* 全局也有预编译，生成的是GO对象(Global Object) */`
`/* 同时没有第三步: (实参值和形参统一) */`
`/* GO可以看做Window对象 (GO.a == window.a) */`

# 举例
## 1
```javascript
function foo(a,b){
    console.log(a);
    c = 0;
    var c;
    a = 3;
    b = 2;
    console.log(b);
    function b() {}
    function d() {}
    console.log(b);
}
test(1);
```

1.创建AO对象 - Activation Object (执行期上下文)
2.找形参和变量声明，将形参和变量名作为AO属性名，值为undefined
3.将实参值和形参统一
4.找函数体里面的函数声明，然后值赋予函数体

1.
```javascript
AO {}
```

2.
```javascript
AO{
    a: undefined,
    b: undefined,
    c: undefined
}
```

3.
```javascript
AO{
    a: 1,
    b: undefined,
    c: undefined
}
```

4.
```javascript
AO{
    a: 1,
    b: function b() {}
    c: undefined,
    d: function d() {}
}
```

```
1
2
2
```

## 2
```javascript
global = 100;
function fn() {
    console.log(global);
    global = 200;
    console.log(global);
    var global = 300;
}
fn();
var global;
```

```javascript
// 开始预编译, 找到变量声明
// 找到函数声明, 然后赋值
GO {
    global: undefined,
    fn: function fn() { ... }
}

//预编译完成, 执行
//global = 100
GO {
    global: 100,
    fn: function fn() { ... }
}

//fn()
//开始函数中的预编译
GO { ... }
AO {}

//找到变量声明
AO{
    global: undefined
}

//没有形参, 没有函数声明, 略过

// console.log(global)
//从AO中找到global并打印, 输出undefined

// global = 200
AO{
    global: 200
}

// console.log(global)
//从AO中找到global并打印, 输出200

// var global = 300
AO{
    global: 300
}

//函数执行结束

// var global
// 预编译已定义, 略过
```