---
title: '[笔记]SpringCloud学习00: 微服务与SpringCloud'
date: 2020-03-11 21:26:57
tags: [Java,SpringCloud]
categories: [编程,微服务,SpringCloud]
---

# 微服务是什么
> 　　`微服务`是一种架构模式，它提倡将`单一`应用程序划分为一组小的服务，服务之间相互协调、相互配合，为用户提供最终价值。每个服务运行在其`独立`的`进程`中，服务间采用轻量级的通信机制互相协作(例如基于HTTP协议的RESTful API)。  
　　每个服务都围绕具体业务进行构建，并能够`独立`的被部署到生产环境、类生产环境等。另外，应尽量避免统一的、集中式的服务管理机制，对具体的一个服务而言，应根据业务上下文，选择合适的语言、工具对其进行构建。

## 微服务架构应满足哪些维度
- 服务的注册与发现
- 服务调用
- 服务熔断
- 负载均衡
- 服务降级
- 服务消息队列
- 配置中心管理
- 服务网关
- 服务监控
- 全链路追踪
- 自动化构建部署
- 服务定时任务调度操作

# Spring Cloud 是什么
> 融合、协调、组装微服务相关技术，使构建分布式系统变更更容易。是分布式微服务架构的一种技术体现，是多种微服务架构落地技术的集合体。(微服务全家桶)

<!-- more -->

### Spring Cloud 版本命名规则
`Spring Cloud`是一个由许多`子项目`组成的`综合`项目，各子项目有不同的发布节奏。为管理Spring Cloud 与各子项目的版本依赖关系，发布了一个清单，其中包括了某个Spring Cloud 版本对应的子项目版本。

为避免Spring Cloud 的版本号与子项目版本号混淆，Spring Cloud 采用了英国伦敦地铁站的名称来命名，以字母A-Z的形式来发布迭代版本，也就是说Spring Cloud 是以字母作为版本号。

当Spring Cloud 的发布内容**累积到临界点**或**一个重大BUG被解决**后，会发布一个`service releases` 版本，简称`SR`版本(SR1、SR2等)。

## Spring Cloud 和Spring Boot 的版本对应
在[https://spring.io/projects/spring-cloud](https://spring.io/projects/spring-cloud)网页底部可查看SpringCloud和SpringBoot的版本对应关系

![SpringCloud和SpringBoot对应版本](https://s1.ax1x.com/2020/03/11/8VEh8g.png)

更详细的依赖信息需要在[https://start.spring.io/actuator/info](https://start.spring.io/actuator/info)中查讯

在[https://spring.io/projects/spring-cloud#learn](https://spring.io/projects/spring-cloud#learn)页面中找到Spring Cloud 的`Reference Doc`在这个页面里可以找到官方推荐的`Spring Boot`版本