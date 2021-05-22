---
title: '[笔记]SpringCloud学习07: Eureka自我保护'
date: 2020-03-20 03:02:19
tags: [Java,SpringCloud,Eureka]
categories: [编程,微服务,SpringCloud]
---

# Eureka 自我保护机制
保护模式主要用于一组客户端和服务端之间存在网络分区场景下的保护，一旦进入`保护模式`，`Eureka Server`会尝试保护其服务注册表中的信息，将**不再删除**服务注册表中的信息，也就是**不会注销任何微服务**

> 一句话概括: 如果某一时刻，`Eureka Server`中某个微服务不可用了，`Eureka`不会立即清除，而是会尝试对该微服务的信息进行保存 (CAP中的AP)

# 为什么需要自我保护机制
> 为了防止`Eureka Client`可以正常运行，但与`Eureka Server`直接网络不同的情况下，`Eureka Server`错误地将可以正常运行的`Client`端移除

默认情况下，如果`Eureka Server`在一定时间内(默认90s)没有接收到某个服务实例的心跳时，`Eureka Server`会注销该实例。但当网络分区故障发生时(延时，拥挤)，微服务与`Eureka Server`之间无法正常通讯时，超时移除节点是不正确的行为——微服务自身很可能是正常状态，也就是说不应该注销此服务。

`Eureka`通过`自我保护模式`来解决这个问题——当`Eureka Server`短时间内丢失过多`Client`端时，那么这个节点将进入自我保护模式。

也就是说宁可保留错误的服务注册信息，也不会盲目注销任何可能正常的服务实例

# 如何判断Eureka 是否进入了自我保护模式
如果在`Eureka Server`首页看到以下提示，则说明`Eureka`进入了保护模式

> <span style="color:#D88">EMERGENCY! EUREKA MAY BE INCORRECTLY CLAIMING INSTANCES ARE UP WHEN THEY'RE NOT. RENEWALS ARE LESSER THAN THRESHOLD AND HENCE THE INSTANCES ARE NOT BEING EXPIRED JUST TO BE SAFE.</span>

<!-- more -->

# 如何禁止自我保护机制
在Erueka Server 端的`application.yml`文件中，添加以下内容
```yml
eureka:
  server:
    # 关闭自我保护机制
    enable-self-preservation: false
    # 移除服务的超时时间，单位毫秒
    eviction-interval-timer-in-ms: 15000
```

在Eureka Client 端的`application.yml`文件中，添加以下内容
```yml
eureka: # eureka 客户端
  instance:
    # Eureka Client 端向Server 端发送心跳的时间间隔，单位秒，默认30s
    # 修改心跳时间主要是因为修改了Server 端的超时时间(15000ms = 15s)
    # 所以对应的修改Client 端的心跳时间
    lease-renewal-interval-in-seconds: 5
    # Eureka Server 端在收到最后一次心跳后等待时间上限，单位秒，超时将剔除服务，默认90s
    lease-expiration-duration-in-seconds: 15
```
