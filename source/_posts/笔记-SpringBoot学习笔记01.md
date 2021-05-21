---
title: '[笔记]SpringBoot学习笔记01'
date: 2018-09-11 10:08:00
tags: [Java,SpringBoot]
categories: [学习笔记,Springboot]
---

# Spring学习笔记
环境配置:  
JDK: Java 8  
IDE: Intellij IDEA  
服务器: Tomcat 9

## 在IDEA中创建Spring项目
- Create Project -> Spring Initializr
  1. 选择JDK
  2. 选择Initializr Service URL (https://start.spring.io)
  3. 选择插件
    - Core -> DevTools
    - Web -> web
    - Template Engines -> Thymeleaf
  4. 完成

## 创建Controller来控制地址映射
1. 创建容纳Controller的包和类
2. 添加注解`@Controller`
3. 添加路径映射`@RequestMapping`
```java
    @Controller
    //此路径将作为此类里面所有方法的根目录
    @RequestMapping(value = {"/home"})
    public class HomePage {}
```

<!-- more -->

4. 添加方法
```java
    @Controller
    @RequestMapping(value = {"/home"})
    public class HomePage {
        //此路径作为此方法的映射路径
        @RequestMapping(value = {"/page"})
        public String Page1(){
            //此处return的字符串即为templates文件夹下的html文件名
            return "p1";
        }
    }
```

5. 创建模板
在resources/templates/文件夹下创建html文件, 文件名与方法中return的值相同
6. 启动服务器
7. 在浏览器地址栏输入`localhost:8080/home/page`来访问页面

## 使用ResponseBody来进行内容输出
1. 添加方法
2. 添加`@RequestMapping`和`@ResponseBody`
```java
    @Controller
    @RequestMapping(value = {"/home"})
    public class HomePage {
        @RequestMapping(value = {"/res"})
        @ResponseBody
        public String ResponseBody(){
            String str = "ReturnResponseBodyText";
            return str;
        }
    }
```

3. 启动服务器
4. 在浏览器地址栏输入`localhost:8080/home/res`来访问页面