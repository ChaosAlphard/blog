---
title: '[笔记]SpringCloud学习03: Eureka的搭建'
date: 2020-03-16 00:36:52
tags: [Java,SpringCloud,Eureka]
categories: [编程,微服务,SpringCloud]
---

# Eureka Server端的搭建
创建一个`Maven`项目，在`pom.xml`中写入以下依赖:
```xml
<dependency>
  <groupId>org.springframework.cloud</groupId>
  <artifactId>spring-cloud-starter-netflix-eureka-server</artifactId>
  <!-- 版本从父工程的dependencyManagement中继承, 故此处不用写 -->
</dependency>
```

创建启动类
```java
package com.ilirus.springcloud;

import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;
import org.springframework.cloud.netflix.eureka.server.EnableEurekaServer;

@SpringBootApplication
@EnableEurekaServer
public class EurekaServerMain7001 {
    public static void main(String[] args) {
        SpringApplication.run(EurekaServerMain7001.class, args);
    }
}
```

配置`yml`
```yml
server:
  port: 7001

eureka: # eureka 服务端
  instance:
    hostname: localhost # eureka服务端实例名称
  client:
    register-with-eureka: false # 是否向服务中心注册自己
    feth-registry: false # 是否向服务中心检索已有的注册信息
    service-url:
      # 设置Eureka Server 的交互地址。查询服务和注册服务都需要依赖这个地址
      defaultZone: http://${eureka.instance.hostname}:${server.port}/eureka/
```

启动工程并查看是否有误

<!-- more -->

# Eureka Client端的注册
回到之前的子项目中，向`pom.xml`中添加以下依赖
```xml
<dependency>
  <groupId>org.springframework.cloud</groupId>
  <artifactId>spring-cloud-starter-netflix-eureka-client</artifactId>
</dependency>
```

修改`application.yml`文件
```yml
spring:
  application:
    name: irs-provider-payment8001 # 名称

eureka: # eureka 客户端
  client:
    register-with-eureka: true # 向服务中心注册自己
    fetch-registry: true # 向服务中心获取已有的注册信息, 集群必须设置为true才能配合ribbon使用负载均衡
    service-url:
      # 服务中心链接
      defaultZone: http://localhost:7001/eureka
```

修改启动类并添加`@EnbaleEurekaClient`注解
```java
package com.ilirus.springcloud;

import org.mybatis.spring.annotation.MapperScan;
import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;
import org.springframework.cloud.netflix.eureka.EnableEurekaClient;

@SpringBootApplication
@MapperScan("com.ilirus.springcloud.dao")
@EnableEurekaClient
public class PaymentMain8001 { ... }
```

启动工程，刷新eureka管理网页，查看服务是否成功注册服务中心
![管理页面](https://s1.ax1x.com/2020/03/16/88qHv4.jpg)

# 微服务显示信息完善
在对应工程中(Eureka Client 端)的`application.yml`文件中添加以下内容
```yml
eureka:
  instance:
    # 实例的显示id
    instance-id: projectName
    # 显示ip地址
    prefer-ip-address: true
```
