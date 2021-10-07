---
title: "单例模式"
description:
date: 2021-09-21T22:03:11+08:00
image:
math:
toc: true
license:
hidden: false
comments: true
draft: true
tags: [Java,设计模式]
categories: [编程]
---

# 单例模式

> 单例模式（Singleton Pattern）是 Java 中最简单的设计模式之一。这种类型的设计模式属于创建型模式，它提供了一种创建对象的方式。  
> 这种模式涉及到一个单一的类，该类负责创建自己的对象，同时确保只有单个对象被创建。这个类提供了一种访问其唯一的对象的方式，可以直接访问，不需要实例化该类的对象。

# 实现
## 懒汉式
- 需要时加载
- 线程不安全
```java
public class Singleton {  
  private static Singleton instance;
  private Singleton() {}

  public static Singleton getInstance() {
    if (instance == null) {
      instance = new Singleton();
    }

    return instance;
  }
}
```

## 饿汉式
- 启动时加载
```java
public class Singleton {  
  private static Singleton instance = new Singleton();
  private Singleton() {}

  public static Singleton getInstance() {
    return instance;
  }
}
```

## 双检锁
即 Double-Checked Locking
- 需要时加载
- 线程安全
- 多线程下保持较高性能
```java
public class Singleton {
  private volatile static Singleton singleton;
  private Singleton() {}

  public static Singleton getSingleton() {
    if (singleton == null) {
      synchronized (Singleton.class) {
        if (singleton == null) {
          singleton = new Singleton();
        }
      }
    }

    return singleton;
  }
}
```

## 枚举
- 启动时加载
- 线程安全
- 防止反序列化重新创建对象
```java
public enum Singleton {  
  INSTANCE;

  public void method() {
    // ...
  }
}
```
