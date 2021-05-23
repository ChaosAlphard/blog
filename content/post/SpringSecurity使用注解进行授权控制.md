---
title: SpringSecurity使用注解进行授权控制
date: 2020-04-04 02:10:29
tags: [Java,SpringSecurity]
categories: [编程,SpringSecurity]
---

# 使用注解进行授权控制

> 从`SpringSecurity`2.0版本开始，支持服务层方法的安全性支持，目前有`@Secured`，`@PreAuthorize`，`@PostAuthorize`三类注解

在`@Configuration`实例上使用`@EnableGlobalMethodSecurity`注解来启用基于注解的授权控制
```java
@Configuration
@EnableWebSecurity
// 启用方法授权
@EnableGlobalMethodSecurity(
    // 启用@Secured注解
    securedEnabled = true,
    // 启用@PreAuthorize和@PostAuthorize注解
    prePostEnabled = true
)
public class SecurityConfig extends WebSecurityConfigurerAdapter { ... }
```

之后向方法(类、方法或者接口)添加注解就会限制对该方法的访问

## @Secured注解
```java
@RestController
public class IndexController {
    // IS_AUTHENTICATED_ANONYMOUSLY: 表示可以匿名访问
    @Secured("IS_AUTHENTICATED_ANONYMOUSLY")
    @RequestMapping("/r")
    public String resource() {
        return getUserName()+"访问资源";
    }

    // ROLE_USER: 表示需要有USER权限才能访问
    @Secured("ROLE_USER")
    @RequestMapping("/r1")
    public String resource1() {
        return getUserName()+"访问资源1";
    }
}
```

<!-- more -->

## @PreAuthorize和@PostAuthorize注解
```java
@RestController
public class IndexController {
    // @PreAuthorize 会在方法执行前进行权限验证
    // isAnonymous() 允许匿名访问
    @PreAuthorize("isAnonymous()")
    @RequestMapping("/pre/a")
    public String preAssets() {
        return getUserName()+"访问preAssets";
    }

    // 拥有USER 权限才可访问
    @PreAuthorize("hasRole('USER')")
    @RequestMapping("/pre/a1")
    public String preAssets1() {
        return getUserName()+"访问preAssets1";
    }

    // @PostAuthorize 会在方法执行后进行权限验证
    // 拥有USER 和ADMIN 任一权限即可访问
    @PostAuthorize("hasAnyRole('USER', 'ADMIN')")
    @RequestMapping("/post/a1")
    public String postAssets1() {
        System.out.println("访问postAssets1");
        return getUserName()+"访问postAssets1";
    }

    // 同时拥有USER 和ADMIN 权限才可访问
    @PostAuthorize("hasRole('USER') and hasRole('ADMIN')")
    @RequestMapping("/post/a2")
    public String postAssets2() {
        System.out.println("访问postAssets2");
        return getUserName()+"访问postAssets2";
    }
}
```
