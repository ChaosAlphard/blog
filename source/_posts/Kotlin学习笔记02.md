---
title: Kotlin学习笔记02
date: 2018-12-06 15:08:02
tags: [Kotlin]
categories: [学习笔记,Kotlin]
---

# Kotlin基本语法
## 变量、常量的定义
`[修饰符] <var|val> <名称> : <类型> = <赋值>`

```kotlin
//var 代表变量，可被更改
var age: Int = 18
//val 代表常量, 定义后不能被修改, 定义时必须赋值
val name: String = "Ilirus"
```

## 基本数据类型
```kotlin
var boolean:Boolean = false
var char:Char = 'a'
var byte:Byte = 127
var short:Short = 32767
var int:Int = 2147483647
var long:Long = 9223372036854775807
var float:Float = 2.33f
var double:Double = 3.1415926
```

<!-- more -->

## 类型检测与自动转换
```kotlin
fun getStrLength(o: Any): Int {
    if(o is String){
        //进行类型判断后, o会自动被转换为String类型
        return o.length
    }
    return 0
}
```

## 返回空值与空值检测
如果引用或函数的返回值可能为空值，则必须显式标记nullable
```kotlin
//在类型后面加上一个问号, 表示这个对象可能为空
//用两个感叹号表示一定不为空
fun getStuName(stu: Any?): String? {
    if(stu!=null){
        return stu.getName
    }
    return null
}
```

Kotlin的空值检测能有效的避免`NPE`(NullPointException) 的出现

例如这句代码`student?.getName()`，只会在student不为空时执行  
相反，这句代码`student?:student=Student()`，只会在student为空时执行

如果要执行代码块的话，可以这样写
```kotlin
//data不为空执行
data?.let{
    // do something
}

//data为空执行
data?:let{
    // do something
}

//或者

//data不为空执行
data?.run{
    // do something
}

//data为空执行
data?:run{
    // do something
}
```

## run与let
```kotlin
val str:String = "Ilirus"

str?.let{
    println(it)
}
str?.let{ s ->
    println(s)
}
str?.run{
    println(this)
}
```

## `when`关键词
when类似于Java中的switch
```kotlin
fun printType(o:Any) {
    when(o){
        is Int -> {
            println("Int")
            println(o)
        }
        is String -> {
            println("String")
            println(o.toUpperCase())
        }
        is Char -> println("Char")
        else -> println("Other")
    }
}
```

## `in`关键词
判断数字是否在某一区间内
```kotlin
if( x in 1..100){
    println("yes")
}

if( x !in 1..100){
    println("no")
}
```

遍历集合
```kotlin
for(item in arr){
    println("item: $item")
}

for((index,item) in arr.withIndex()){
    println("$index - $item")
}

//判断集合中是否含有指定对象
if(student in person){
    println("yes")
}
```

## 函数
### 函数的声明
```kotlin
//[修饰符] fun <函数名>([参数]) : <返回类型>
fun sum(a: Int, b: Int): Int {
    return a+b
}

//方法体只有一条语句时可简化
fun sum(a: Int, b: Int) = a+b

//方法为public时, 必须显式声明返回类型
public fun sum(a: Int, b: Int): Int = a+b
```

不返回值
```kotlin
fun printSum(a: Int, b: Int): Unit {
    println(a+b)
}

//可省略Unit关键词, 即使方法是public修饰的
public printSum(a: Int, b: Int) {
    println(a+b)
}
```

### 函数默认参数
```kotlin
fun hello(name: String = "User"): String = "Hello $name"
```
直接调用`hello()`，将得到默认字符串`"Hello User"`  
也可以自己传入参数`hello("Ilirus")`，来得到`"Hello Ilirus"`

如果函数有较多的参数时，可以使用多行参数的写法
```kotlin
fun hello(name: String = "User",
          sid: Int = 0): String {
    return "Hello $name Your SID is $sid"
}
```

### 变参函数
使用`vararg`来表示这是一个变长参数
```kotlin
fun hasEmpty(vararg array: Any?): Boolean {
    return array.any{ it == null || "".equals(it) }
}
```

### 扩展函数
给目标类添加一个方法，使其可以在当前类中被使用

receiverType: 表示函数的接收者，也就是函数扩展的对象  
functionName: 扩展函数的名称  
params: 扩展函数的参数，可以为NULL

```kotlin
fun receiverType.functionName(params){
    //body
}
```

例如
```kotlin
//定义一个User类
class User(
    var name: String? = null
)

//在另一个类(当前类)中扩展User类
fun User.setName(name: String){
    this.name = name
}

fun User.getName(): String? {
    return this.name
}

//在当前类中执行User类的扩展方法
fun main(arg:Array<String>){
    var usr = User()
    println(usr.setName("Ilirus"))
    println(usr.getName())
}
```

#### 注意事项
**其实扩展函数是静态解析的，而并不是真的给类添加了方法**
```kotlin
open class Animal{
    //
}

class Dog:Animal()

object Main {
    fun Animal.bark() = "animal"

    fun Dog.bark() = "dog"

    fun Animal.printBark(anim: Animal){
        println(anim.bark())
    }

    @JvmStatic fun main(args: Array<String>) {
        Animal().printBark(Dog())
    }
}
```

最终输出的是`animal`，而不是`dog`

因为扩展方法是静态解析的，在添加扩展方法的时候类型为`Animal`，那么即便运行时传入了子类对象，也依旧会执行参数中声明时类型的方法。 

### 函数入参
在Kotlin中，可以将一个函数作为参数传递给另一个函数
```kotlin
fun checkEach(array: Array<Any>, callback: (Any) -> Unit): Unit {
    for(arr in array){
        callback(arr)
    }
}

var method:(Any)->Unit = {
    println(it)
}

checkEach(array, method)
```