---
title: '[笔记]SpringBoot学习笔记03'
date: 2018-09-28 10:23:40
tags: [Java,Thymeleaf]
categories: [学习笔记,Springboot]
---

# Thymeleaf网页模板
## 使用
控制器方法中添加`Model`
```java
//import org.springframework.ui.Model;
@RequestMapping(value = {""})
private String DefPage(Model model){
    String name = "Ilirus";
    model.addAttribute("name",name);
    return "def";
}
```

HTML中引入命名空间之后使用
```html
<html xmlns:th="http://www.thymeleaf.org">
<head>
    <meta charset="UTF-8">
    <title>Title</title>
</head>
<body>
    <a href="#" th:text="${name}">被替换的文本</a>
</body>
</html>
```

<!-- more -->

## 语法
- 替换文本
`<div th:text="'替换: '+${name}">被替换的文本</div>`
- 替换href
`<a th:href="@{'localhost:8080/'+${url}}"></a>`
- 循环遍历
```html
<div th:each="li:${list}">
    <a th:href="@{'localhost:8080/'+${li.url}}" th:text="${li.text}"></a>
</div>
```
- 引入模板
  - 定义模板
    `<th:block th:fragment="domblock">Dom Element here</th:block>`
    `<div th:fragment="nav">Dom Element here</div>`
  - 引入模板
    `<th:block th:insert="path/file::domblock"></th:block>`
    `<div th:insert="path/file::nav"></div>`
    - th:insert
    - th:replace
    - th:include




## 使用webjar引入bootsrtap
在`http://mvnrepository.com/`网站中找到Bootstrap
复制Maven配置代码到pom.xml的`<dependencies>`里
```xml
<!-- https://mvnrepository.com/artifact/org.webjars/bootstrap -->
<dependency>
    <groupId>org.webjars</groupId>
    <artifactId>bootstrap</artifactId>
    <version>3.3.7</version>
</dependency>
```
完成后编译器会导入Maven依赖