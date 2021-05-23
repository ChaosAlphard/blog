---
title: Maven中dependencyManagement元素的使用
date: 2020-03-12 17:16:24
tags: [Maven]
categories: [编程,Maven]
---

# dependencyManagement元素的使用
使用`dependencyManagement`元素能让所有在子项目中引用一个依赖而不用显式的列出版本号，Maven会沿父子层**向上**查找，直到找到一个拥有`dependencyManagement`元素的项目，然后它就会使用这个`dependencyManagement`中指定的版本号

例如在父项目中:
```xml
<dependencyManagement>
  <dependencies>
    <dependency>
      <groupId>mysql</groupId>
      <artifactId>mysql-connector-java</artifactId>
      <version>8.0.14</version>
    </dependency>
  </dependencies>
</dependencyManagement>
```

在子项目里:
```xml
<dependencies>
  <dependency>
    <groupId>mysql</groupId>
    <artifactId>mysql-connector-java</artifactId>
  </dependency>
</dependencies>
```

这样做的好处是，如果多个子项目都引用同一个依赖，则可以统一对子项目依赖进行管理版本

<!-- more -->

> 如果某个子项目需要另一个版本，那么只需要在那个子项目依赖中声明version即可
```xml
<dependencies>
  <dependency>
    <groupId>mysql</groupId>
    <artifactId>mysql-connector-java</artifactId>
    <version>5.1.48</version>
  </dependency>
</dependencies>
```

> - 父项目中的`dependencyManagement`里只是**声明**依赖，**并不实现引入**，因此子项目需要显式声明需要用的依赖。
>   - 如果需要所有子项目都继承某个依赖，则需要在父项目中使用`dependencies`，需要注意的是`dependencies`要放在`dependencyManagement`上方
> - 如果**不在**子项目中声明依赖，则**不会**从父项目中继承，只有在子项目中声明了依赖，且没有指定具体版本，才会从父项目中继承，并且`version`和`scope`都读取自父pom文件
> - 如果子项目中指定了版本号，那么会使用子项目中指定的jar版本