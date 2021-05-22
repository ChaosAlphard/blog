---
title: '[笔记]SpringCloud学习08: Ribbon基础'
date: 2020-03-21 03:44:07
tags: [Java,SpringCloud,Ribbon]
categories: [编程,微服务,SpringCloud]
---

# Ribbon 介绍
> `Ribbon`是`Netflix`发布的一款用于负载均衡的开源项目，是一个客户端的负载均衡器。

`Ribbon`会基于某些规则(轮询，随机等)决定要调用的服务链接，并且可以使用`Ribbon`实现自定义的负载均衡算法。

## 负载均衡是什么
负载均衡(Load Balance)简单来说就是将用户的请求平摊到各个服务器上，从而提高服务的承载能力。

## Ribbon 本地负载均衡和Nginx 服务端负载均衡的区别
Nginx: 客户端所有请求都先发给Nginx，让后由Nginx 服务器实现请求转发。(集中式LB)

Ribbon: 在调用微服务接口的时候，由Ribbon 的负载均衡算法决定调用哪个服务接口。(进程内LB)

## 集中式LB 和进程内LB
集中式Load Balancer: 在服务的消费方和提供方之间使用独立的LB 设施，由该设施负责把请求访问通过某种策略转发制服务提供方

进程内Load Balancer: 将LB 逻辑集成到消费方，消费方从服务注册中心获知有那些服务地址可用，然后自己再从这些地址中选择一个合适的服务器

<!-- more -->

# Ribbon 引入
修改`pom.xml`
```xml
<dependency>
  <groupId>org.springframework.cloud</groupId>
  <artifactId>spring-cloud-starter-netflix-ribbon</artifactId>
  <!-- 版本从父工程的dependencyManagement中继承, 故此处不用写 -->
</dependency>
```

`Ribbon`已经集成在`Eureka Client`中，如果`pom.xml`中已经引入`Eureka Client`，则可以不用再引入`Ribbon`

```xml
<dependency>
  <groupId>org.springframework.cloud</groupId>
  <artifactId>spring-cloud-starter-netflix-eureka-client</artifactId>
</dependency>
```

# Ribbon 的使用

使用`@LoadBalancer`注解授予`RestTemplate`负载均衡功能，
```java
@Configuration
public class ApplicationontextConfig {
    @Bean
    @LoadBalanced  // 授予restTemplate负载均衡能力
    public RestTemplate getRestTemplate() {
        return new RestTemplate();
    }
}
```

使用`@Resource`或者`@Autowired` 装配具有负载均衡能力的`RestTemplate`
```java
@RestController
@RequestMapping("/test")
public class TestController {
    private static final String TEST_URL = "http://irs-provider-test/test";

    @Resource
    private RestTemplate restTemplate;

    @GetMapping("/get")
    public CommonResult get() {
        // restTemplate 现在已经具有了负载均衡能力
        return restTemplate.getForObject(TEST_URL+"/get", CommonResult.class);
    }
}
```

# Ribbon 默认算法介绍
- `RoundRobinRule`: 轮询
- `RandomRule`: 随机
- `RetryRule`: 先按照`RoundRobinRule`策略获取服务，如果获取服务失败会在指定时间内重试
- `WeightedResponseTimeRule`: 根据平均响应时间计算所有服务的权重，响应时间越快服务权重越大。如果统计信息不足，则使用`RoundRobinRule`策略。等信息足够，则切换到`WeightedResponseTimeRule`策略
- `ZoneAvoidanceRule`: 复合判断 服务所在的区域的性能 和 服务的可用性 以选择服务器
- `BestAvailableRule`: 会先过滤掉由于多次访问故障而处于断路器跳闸状态的服务，然后选择一个并发量最小的服务
- `AvaliabilityFilteringRule`: 会先过滤由于多次访问故障而处于断路器跳闸的状态的服务 和 并发的连接数量超过阈值的服务，然后对剩余的服务列表进行轮询

# Ribbon 负载规则替换
> 如果多个`Ribbon`客户端需要配置负载规则，则自定义配置类不能放在`@ComponentScan`所能扫描到的包下，否则自定义配置类会被所有的`Ribbon`共享，达不到定制的目的。

创建自定义配置类
```java
@Configuration
public class LoadBalancerRuleConfig {
    @Bean
    public IRule LBRule() {
        // 定义负载均衡规则
        return new RandomRule();
    }
}
```

在启动类中加上`@RibbonClient注解`
```java
@SpringBootApplication
@EnableEurekaClient
// 这里的name 指的是服务提供方的name
// 即当前消费方使用自定义配置与其通信的服务提供方的 spring.application.name 的属性
@RibbonClient(name = "irs-provider-payment", configuration = {LoadBalancerRuleConfig.class})
public class OrderMain80 {
    public static void main(String[] args) {
        SpringApplication.run(OrderMain80.class, args);
    }
}
```
