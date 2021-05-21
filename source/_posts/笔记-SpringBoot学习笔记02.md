---
title: '[笔记]SpringBoot学习笔记02'
date: 2018-09-20 11:34:24
tags: [Java,JPA]
categories: [学习笔记,Springboot]
---

# 使用JPA来链接数据库
环境配置:  
JDK: Java 8  
IDE: Intellij IDEA  
服务器: Tomcat 9

## 创建JPA项目
- Create Project -> Spring Initializr
  1. 选择JDK
  2. 选择Initializr Service URL (https://start.spring.io)
  3. 选择插件
    - Core -> DevTools
    - Web -> web
    - Template Engines -> Thymeleaf
    - SQL -> JPA, MySQL
  4. 完成

## 配置JPA项目
### 配置application.properties文件
application.properties 中配置数据库链接
```properties
spring.jpa.database=mysql
spring.datasource.url=jdbc:mysql://localhost:3306/java1604
spring.datasource.username=java1604
spring.datasource.password=java1604
spring.jpa.hibernate.naming.physical-strategy=org.hibernate.boot.model.naming.PhysicalNamingStrategyStandardImpl
```

<!-- more -->

### 添加数据库链接
View -> Tool Windows -> Database -> 添加Data Source

### 添加Hibernate配置文件
- File -> Project Structure -> Modules
- 添加Hibernate框架
- 添加hibernate.cfg.xml配置文件

### 生成Model类
- View -> Tool Windows -> Persistence
- hibernate.cfg.xml -> Generate -> By Database Schema

### 创建dao查询接口
```java
public interface ModelDao extends CrudRepository<Model,Type> {
    //Do something or do nothing.
}
```

### 创建Controller
```java
@Controller
@RequestMapping(value = {"/"})
public class HomeController {}
```
**使用`@Autowired`装配查询接口**

```java
@Controller
@RequestMapping(value = {"/"})
public class HomeController {
    @Autowired private ModelDao dao;

    @RequestMapping(value = {"/count"})
    @ResponseBody
    public String Query(){
        return String.valueOf(dao.count());
    }
}
```

**访问`localhost:8080/count`来查看结果**

## 数据库操作
### 数据库的查询
```java
@RequestMapping(value = {"/QueryAll"})
@ResponseBody
private Iterable<Custom> Query(){
    return dao.findAll();
}
```

### 数据库的添加
```java
@RequestMapping(value = {"/Add"})
@ResponseBody
public Object Add(String fn, String ln){
    Map<String,String> map = new HashMap<>();
    try {
        Custom c = new Custom();
        c.setFirstName(fn);
        c.setLastName(ln);
        dao.save(c);
        map.put("msg","保存成功");
    } catch (Exception e) {
        e.printStackTrace();
        map.put("msg","保存失败");
    }

    return map;
}
```

### 自定义查询语句
创建接口
```java
@Repository
public interface CustomDaoC extends JpaRepository<Custom,Integer> {
    //Do something or do nothing.
}
```

实现方法
```java
@Repository
public interface CustomDaoC extends JpaRepository<Custom,Integer> {
    //查询
    @Query(value = "select * from Custom ", nativeQuery = true)
    List<Custom> QueryCustomAll();

    //模糊查询
    @Query(value = "select * from Custom where name like concat('%',?1,'%')", nativeQuery = true)
    List<Custom> QueryLike(String name);

    //修改
    @Modifying
    @Query(value = "update custom set lastName='?1' where sid=?2", nativeQuery = true)
    void UpdateCustom(String name, String id);
}
```

>https://stackoverflow.com/questions/21456494/spring-jpa-query-with-like


SSL配置
>https://blog.csdn.net/anla_/article/details/80295982
>https://blog.csdn.net/qq_38380025/article/details/80267312