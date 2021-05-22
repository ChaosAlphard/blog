---
title: '[笔记]SpringCloud学习06: Eureka服务发现'
date: 2020-03-20 02:03:59
tags: [Java,SpringCloud,Eureka]
categories: [编程,微服务,SpringCloud]
---

# 获取Eureka Server 端注册成功的服务信息

> 对于注册进`Eureka`里面的微服务，可以通过**服务发现**来获取该服务的信息

修改对应工程(Eureka Client 端)的Controller
```java
package com.ilirus.springcloud.controller;

import com.ilirus.springcloud.entities.CommonResult;
import com.ilirus.springcloud.enums.Status;
import lombok.extern.slf4j.Slf4j;
import org.springframework.cloud.client.ServiceInstance;
import org.springframework.cloud.client.discovery.DiscoveryClient;
import org.springframework.web.bind.annotation.*;

import javax.annotation.Resource;
import java.util.List;

@RestController
@RequestMapping("/payment")
@Slf4j
public class PaymentController {
    @Resource
    private DiscoveryClient discoveryClient;

    @GetMapping("/discovery")
    public CommonResult discovery() {
        List<String> services = discoveryClient.getServices();
        return CommonResult.ofData(Status.SUCCESS, services);
    }

    @GetMapping("/discovery/{instances}")
    public CommonResult discovery(@PathVariable String instances) {
        List<ServiceInstance> serviceInstances = discoveryClient.getInstances(instances);
        return CommonResult.ofData(Status.SUCCESS, serviceInstances);
    }
}
```

<!-- more -->

修改启动类
```java
package com.ilirus.springcloud;

import org.mybatis.spring.annotation.MapperScan;
import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;
import org.springframework.cloud.client.discovery.EnableDiscoveryClient;
import org.springframework.cloud.netflix.eureka.EnableEurekaClient;

@SpringBootApplication
@MapperScan("com.ilirus.springcloud.dao")
@EnableEurekaClient
@EnableDiscoveryClient
public class PaymentMain8001 { ... }
```

![](https://s1.ax1x.com/2020/03/20/86aIns.png)

![](https://s1.ax1x.com/2020/03/20/86aoBn.png)
