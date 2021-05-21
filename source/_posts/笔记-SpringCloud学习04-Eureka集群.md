---
title: '[笔记]SpringCloud学习04: Eureka集群'
date: 2020-03-17 23:33:16
tags: [Java,SpringCloud,Eureka]
categories: [学习笔记,SpringCloud]
---

# Eureka 集群原理

我们假设有三台 Eureka Server 组成的集群，只要三个地方的任意一个Eureka Server不出现问题，都不会影响整个架构的稳定性。

![Eureka集群](https://s1.ax1x.com/2020/03/19/8yJLwT.jpg)

从图中可以看出`Eureka Server`集群相互之间通过`Replicate`来同步数据，相互之间不区分主节点和从节点，所有的节点都是平等的。在这种架构中，节点通过**彼此互相注册**来提高可用性，每个节点需要添加一个或多个有效的serviceUrl 指向其他节点。

如果某台Eureka Server 宕机，Eureka Client 的请求会自动切换到新的Eureka Server 节点。当宕机的服务器重新恢复后，Eureka 会再次将其纳入到服务器集群管理之中。当节点开始接受客户端请求时，所有的操作都会进行节点间复制，将请求复制到其它Eureka Server 当前所知的所有节点中。

另外Eureka Server 的同步遵循着一个非常简单的原则：只要有一条边将节点连接，就可以进行信息传播与同步。所以，如果存在多个节点，只需要将节点之间两两连接起来形成通路，那么其它注册中心都可以共享信息。每个Eureka Server 同时也是Eureka Client，多个Eureka Server 之间通过`P2P`的方式完成服务注册表的同步。

Eureka Server 集群之间的状态是采用异步方式同步的，所以不保证节点间的状态一定是一致的，不过基本能保证最终状态是一致的。

Eureka保证AP (CAP原则又称CAP定理，指的是在一个分布式系统中，一致性(Consistency)、可用性(Availability)、分区容错性(Partition tolerance))

Eureka Server 各个节点都是平等的，几个节点挂掉不会影响正常节点的工作，剩余的节点依然可以提供注册和查询服务。而Eureka Client 在向某个Eureka 注册时，如果发现连接失败，则会自动切换至其它节点。只要有一台Eureka Server 还在，就能保证注册服务可用(保证可用性)，只不过查到的信息可能不是最新的(不保证强一致性)。

<!-- more -->

# Eureka 集群搭建

新建一个Eureka Server，并修改`application.yml`文件
```yml
server:
  port: 7002

eureka:
  instance:
    hostname: eureka7002.com
  client:
    register-with-eureka: false
    feth-registry: false
    service-url:
      # 相互注册
      defaultZone: http://eureka7001.com:7001/eureka/
```

修改之前的单机eureka配置文件
```yml
server:
  port: 7001

eureka:
  instance:
    hostname: eureka7001.com
  client:
    register-with-eureka: false
    feth-registry: false
    service-url:
      # 相互注册
      defaultZone: http://eureka7002.com:7002/eureka/
```

> 因为是在同一台机器上，所以需要修改host文件将`eureka7001.com`和`eureka7002.com`映射为`127.0.0.1`

# 微服务注册进集群
回到之前的子工程中，修改`application.yml`文件中的eureka配置
```yml
eureka: # eureka 客户端
  client:
    register-with-eureka: true
    fetch-registry: true
    service-url:
      # 服务中心链接
      defaultZone: http://eureka7001.com:7001/eureka,http://eureka7002.com:7002/eureka
```

启动服务，查看是否正确
![集群](https://s1.ax1x.com/2020/03/19/8y5eZ6.png)
