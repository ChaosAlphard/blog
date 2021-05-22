---
title: '[笔记]SpringBoot学习04'
date: 2018-10-16 09:10:46
tags: [Java,SpringBoot]
categories: [编码]
---

# Spring打包和自定义日志
## Spring打包
### jar包
View->Tool Windows->Maven Project  
展开Lifecycle  
右键package  
Run Maven Build  
在`target`文件夹中找到打包出来的`.jar`文件  
使用`java -jar <jarName>` 命令启动

<!-- more -->

### war包
修改pom.xml
`<packaging>jar</packaging>` 改为 `<packaging>war</packaging>`

`SpringBootApplication`类 改为
```java
public class ClassName extends SpringBootServletInitializer {

    @Override
    protected SpringApplicationBuilder configure(SpringApplicationBuilder builder){
        return builder.sources(ClassName.class);
    }

    public static void main(String[] args) {
        SpringApplication.run(ClassName.class, args);
    }
}
```

再按照打包jar的方式打包

## 日志
定义log变量  
`import org.apache.commons.logging.Log`  
`private Log log = LogFactory.getLog(className.class);`  
输出log
```java
log.debug("输出debug信息")
log.info("输出信息");
log.warn("输出警告");
log.error("输出错误")
log.fatal("输出致命错误")
```