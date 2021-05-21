---
title: '[笔记]SpringCloud学习02: Eureka基础'
date: 2020-03-14 03:28:00
tags: [Java,SpringCloud,Eureka]
categories: [学习笔记,SpringCloud]
---

# 什么是服务治理
当我们的服务与服务之间通讯调用的时候，如果简单粗暴的用httpclient去请求，不经过第三方的组件管理，然后访问路径写死，那么如果有一个服务更换了路径，所有的服务都需要手动更换该服务的访问路径。维护的成本很高。

就是因为出现了这些问题，所以引入了服务治理(SOA governance)这个概念，同时衍生了这类的实现组件，也就是注册中心。典型的代表：zookeeper，Eureka，Consul。服务治理也引入了 服务调用、负载均衡、容错等功能。

> 简单的总结：把服务看成一个地方。当我们服务调用的时候，就是从一个地方到另一个地方，这时候我们需要地图导航才能到达，当我们去的路程有一段路可能修路，无法通过的时候，导航就会帮我们规划另一条路线。这里的导航就是注册中心，会一定的时间就会刷新一下路况，就好像我们注册中心 (Erueka为例) 每隔30秒就会刷新一下服务的地址。

# Eureka设计
`Eureka`采用了CS的设计构架，`Eureka Server`作为服务注册功能的服务器，它是服务注册中心。而系统中的其他微服务，使用`Eureka`的客户端连接到`Eureka Server`中心并维持`心跳连接`。这样系统的维护人员就可以通过`Eureka Server`来监控系统中各个微服务是否运行正常。

当服务器(服务提供方)启动的时候，会把当前自己服务器的信息(服务地址、通信地址等)以别名的方式注册到注册中心上。另一方(服务消费方)以该别名的方式去注册中心上获取到实际的服务器通讯地址，然后再实现本地RPC调用远程RPC。调用框架核心在于注册中心，注册中心管理每个服务与服务间的依赖关系(服务治理)，也就是说，在任何RPC远程框架中，都会有一个注册中心。

![Eureka](https://s1.ax1x.com/2020/03/14/8MtKwd.jpg)
- Eureka Server: 提供服务注册和发现，多个Eureka Server之间会同步数据，做到状态一致(最终一致性)
- Service Provider: 服务提供方，将自身服务注册到Eureka，从而使服务消费方能够找到
- Service Consumer: 服务消费方，从Eureka获取注册服务列表，从而能够消费服务
<!-- ![Dubbo](https://s1.ax1x.com/2020/03/14/8MtueH.jpg) -->

<!-- more -->

# Eureka组件
`Eureka`包含两个组件：`Eureka Server`和`Eureka Client`

## Eureka Server

`Eureka Server`提供注册服务，各个微服务节点通过配置启动后，会在`Eureka Server`中进行注册，这样`Eureka Server`中的服务注册表会储存所有可用服务节点的信息，服务节点的信息可在管理界面中直观的看到。

**`Eureka Server` 同时也是一个`Eureka Client`** 在不禁止Eureka Server的客户端行为时，它会向它配置文件中的其他Eureka Server 进行拉取注册表、服务注册和发送心跳等操作。

## Eureka Client

`Eureka Client` 可向注册中心进行访问来获取服务提供方通讯地址。在启动后，`Eureka Client`将会向`Eureka Server`发送心跳(默认间隔为30s)。如果`Eureka Server`在多个心跳周期内没有接收到某个节点的心跳，`Eureka Server`则会将该节点从服务器注册表中移除(默认90s)。

### 服务提供方
- 启动后，向注册中心发起`register`请求，注册服务
- 在运行过程中，定时向注册中心发送`renew`心跳，证明"我还活着"
- 停止服务提供者，向注册中心发起`cancel`请求，清空当前服务注册信息

### 服务消费方
- 启动后，从注册中心拉取服务注册信息
- 在运行过程中，定时更新服务注册信息
- 发起向服务提供方远程调用时
