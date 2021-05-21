---
title: '[笔记]SpringCloud学习09: OpenFeign服务调用'
date: 2020-03-29 03:07:11
tags: [Java,SpringCloud,OpenFeign]
categories: [学习笔记,SpringCloud]
---

# Feign 简介
> Feign是一个声明式的`WebService`客户端。它的出现使开发`WebService`客户端变得很简单。使用`Feign`只需要创建一个接口加上对应的注解。
> 
> Feign 是一种声明式、模板化的HTTP 客户端。在SpringCloud 中使用Feign，可以做到使用HTTP 请求访问远程服务，就像调用本地方法一样的，开发者完全感知不到这是在调用远程方法，更感知不到在访问HTTP 请求。

# Feign 和 OpenFeign
`OpenFeign`在`Feign`的基础上支持了`SpringMVC`的注解。

在`OpenFeign`中，除了自身提供的注解之外，还支持使用`JAX-RS`注解，或者`SpringMVC`注解。

`OpenFeign`的`@FeignClient`注解可以解析`SpringMVC`的`@RequestMapping`注解下的接口，并通过动态代理的方式产生实现类，实现类中做负载均衡并调用其他服务。

`OpenFeign`同样使用声明式方式定义Web服务客户端，其次，`OpenFeign`还可以通过集成`Ribbon`或`Eureka`来实现负载均衡的HTTP 客户端。

<!-- more -->

# OpenFeign 的使用
在服务消费方的`pom.xml`中，引入以下依赖
```xml
<dependency>
  <groupId>org.springframework.cloud</groupId>
  <artifactId>spring-cloud-starter-openfeign</artifactId>
</dependency>
```

创建相应的`Service`接口
```java
package com.ilirus.springcloud.service;

import com.ilirus.springcloud.entities.CommonResult;
import com.ilirus.springcloud.entities.Payment;
import org.springframework.cloud.openfeign.FeignClient;
import org.springframework.web.bind.annotation.GetMapping;
import org.springframework.web.bind.annotation.PathVariable;

// value为服务提供方在Eureka中注册的名称
// RequestMapping为提供方对应的请求地址
@FeignClient(value = "irs-provider-payment")
public interface PaymentFeignService {
    @GetMapping("/payment/query/{id}")
    public CommonResult<Payment> getPaymentByID(@PathVariable("id") Long id);
}
```

创建`Controller`
```java
package com.ilirus.springcloud.controller;

import com.ilirus.springcloud.entities.CommonResult;
import com.ilirus.springcloud.entities.Payment;
import com.ilirus.springcloud.enums.Status;
import com.ilirus.springcloud.service.PaymentFeignService;
import lombok.extern.slf4j.Slf4j;
import org.springframework.web.bind.annotation.*;
import org.springframework.web.client.RestTemplate;

import javax.annotation.Resource;

@RestController
@RequestMapping("/consumer")
@Slf4j
public class OrderController {
    @Resource
    private PaymentFeignService paymentFeignService;

    @GetMapping("/payment/query/{id}")
    public CommonResult query(@PathVariable("id") Long id) {
        return paymentFeignService.getPaymentByID(id);
    }
}
```

在启动类中使用`@EnableFeignClients`注解
```java
package com.ilirus.springcloud;

import com.ilirus.springcloud.config.ApplicationontextConfig;
import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;
import org.springframework.cloud.openfeign.EnableFeignClients;

@SpringBootApplication
@EnableFeignClients
public class OrderFeignMain80 { ... }
```
