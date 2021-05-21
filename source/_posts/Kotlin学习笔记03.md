---
title: Kotlin学习笔记03
date: 2018-12-08 20:02:20
tags: [Kotlin]
categories: [学习笔记,Kotlin]
---

# Kotlin中的类
## 类
### 伴生对象
由于Kotlin没有静态方法，在大多数情况下，官方建议简单地使用`包级`函数。  
如果需要一个`可以无需类实例来调用，但需要访问类内部`的函数。  
可以把它写成一个用`companion`修饰的对象内方法。  
`companion`修饰的对象为伴生对象。
```kotlin
companion object {
    fun isEmpty(str: String): Boolean {
        return "" == str
    }
}
```

### 单例类
伴生对象更多的用途是用来创建一个单例类。  
如果只是简单的写法，直接用伴生对象返回一个`val`修饰的外部类对象就可以了，  
但是更多的时候我们希望在类被调用的时候才去初始化他的对象。  
以下代码将线程安全问题交给虚拟机在静态内部类加载时处理:
```kotlin
class Single private constructor() {
    companion object {
        fun get():Single{
            return Holder.instance
        }
    }

    private object Holder {
        val instance = Single()
    }
}
```

<!-- more -->

### 动态代理
Kotlin原生支持动态代理:
```kotlin
interface Animal{
    fun bark()
}

class Dog :Animal {
    override fun bark() {
        println("Wang Wang")
    }
}

class Cat(animal: Animal) : Animal by animal {
    // ...
}

fun main(args: Array<String>) {
    Cat(Dog()).bark()
}
```

### 伪多继承
Kotlin 的动态代理更多的是用在一种需要多继承的场景。

使用一个代理类实现所有需要获取信息的接口方法。然后让不同的子类去实现所需的接口，请求统一交给代理类完成。  
这样不仅维护网络请求信息方便，而且每个类不会有额外多出来的方法。

还是用猫狗来举例:
```kotlin
interface Animal{
    fun bark()
}

interface Food{
    fun eat()
}

class Delegate : Animal, Food {
    override fun eat() {
        println("mouse")
    }

    override fun bark() {
        println("Nyan")
    }
}

class Cat(animal: Animal, food: Food) : Animal by animal, Food by food {
    // ...
}

@JvmStatic fun main(args: Array<String>) {
    val delegate: Delegate = Delegate()
    Cat(delegate, delegate).bark()
}
```