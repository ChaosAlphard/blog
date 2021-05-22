---
title: '[笔记]SpringCloud学习05: 服务提供方集群'
date: 2020-03-19 22:02:13
tags: [Java,SpringCloud,Eureka]
categories: [编程,微服务,SpringCloud]
---

# 服务提供方集群搭建
参考之前搭建好的服务提供方工程，再创建一个新的工程，要注意`application.yml`中，`spring.application.name`属性要和之前的一致，完成后启动服务

![eureka](https://s1.ax1x.com/2020/03/19/86S86P.png)

<!-- more -->

# 服务消费方请求地址修改
在消费方工程中，将请求地址改为微服务名称
```java
package com.ilirus.springcloud.controller;

/* import ... */

@RestController
@RequestMapping("/consumer")
@Slf4j
public class OrderController {
    private static final String PAYMENT_URL = "http://irs-provider-payment/payment";

    @Resource
    private RestTemplate restTemplate;

    @GetMapping("/payment/create")
    public CommonResult create(Payment payment) {
        return restTemplate.postForObject(PAYMENT_URL+"/create", payment, CommonResult.class);
    }

    /* ... */
}
```
因为目前`irs-provider-payment`服务下有两个实例，直接使用服务名并不能让程序知道你想用具体哪一个，所以还需要进一步配置

# RestTemplate 配置负载均衡
修改`RestTemplate`配置类
```java
package com.ilirus.springcloud.config;

import org.springframework.cloud.client.loadbalancer.LoadBalanced;
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;
import org.springframework.web.client.RestTemplate;

@Configuration
public class ApplicationContextConfig {

    @Bean
    @LoadBalanced  // 授予restTemplate负载均衡能力
    public RestTemplate getRestTemplate() {
        return new RestTemplate();
    }
}
```

至此，全部配置完成

![负载均衡](https://s1.ax1x.com/2020/03/19/86kUKA.gif)
