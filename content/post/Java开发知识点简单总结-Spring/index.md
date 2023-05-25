---
title: "Java开发知识点简单总结-Spring"
description:
date: 2023-05-25T20:21:46+08:00
image:
math:
toc: true
license:
hidden: false
comments: true
tags: [Java]
categories: [编程,Java]
---

## Spring
### Spring IoC
IoC (Inverse of Control:控制反转) 是一种设计思想，而不是一个具体的技术实现。IoC 的思想就是将原本在程序中手动创建对象的控制权，交由 Spring 框架来管理。

为什么叫控制反转？控制 ：指的是对象创建（实例化、管理）的权力。反转 ：控制权交给外部环境（Spring 框架、IoC 容器）

将对象之间的相互依赖关系交给 IoC 容器来管理，并由 IoC 容器完成对象的注入。这样可以很大程度上简化应用的开发，把应用从复杂的依赖关系中解放出来。 IoC 容器就像是一个工厂一样，当我们需要创建一个对象的时候，只需要配置好配置文件/注解即可，完全不用考虑对象是如何被创建出来的。

在实际项目中一个 Service 类可能依赖了很多其他的类，假如我们需要实例化这个 Service，你可能要每次都要搞清这个 Service 所有底层类的构造函数，这可能会把人逼疯。如果利用 IoC 的话，你只需要配置好，然后在需要的地方引用就行了，这大大增加了项目的可维护性且降低了开发难度。

在 Spring 中， IoC 容器是 Spring 用来实现 IoC 的载体， IoC 容器实际上就是个 Map（key，value），Map 中存放的是各种对象。

Spring 时代我们一般通过 XML 文件来配置 Bean，后来开发人员觉得 XML 文件来配置不太好，于是 SpringBoot 注解配置就慢慢开始流行起来。

#### 什么是 Spring Bean
简单来说，Bean 代指的就是那些被 IoC 容器所管理的对象。

#### 将一个类声明为 Bean 的注解有哪些
- @Component ：通用的注解，可标注任意类为 Spring 组件。如果一个 Bean 不知道属于哪个层，可以使用@Component 注解标注。
- @Repository : 对应持久层即 Dao 层，主要用于数据库相关操作。
- @Service : 对应服务层，主要涉及一些复杂的逻辑，需要用到 Dao 层。
- @Controller : 对应 Spring MVC 控制层，主要用户接受用户请求并调用 Service 层返回数据给前端页面。

#### @Component 和 @Bean 的区别是什么
- @Component 注解作用于类，而@Bean注解作用于方法。
- @Component通常是通过类路径扫描来自动侦测以及自动装配到 Spring 容器中（我们可以使用 @ComponentScan 注解定义要扫描的路径从中找出标识了需要装配的类自动装配到 Spring 的 bean 容器中）。@Bean 注解通常是我们在标有该注解的方法中定义产生这个 bean,@Bean告诉了 Spring 这是某个类的实例，当我需要用它的时候还给我。
- @Bean 注解比 @Component 注解的自定义性更强，而且很多地方我们只能通过 @Bean 注解来注册 bean。比如当我们引用第三方库中的类需要装配到 Spring容器时，则只能通过 @Bean来实现。

#### @Autowired 和 @Resource 的区别是什么
@Autowired 属于 Spring 内置的注解，默认的注入方式为byType (根据类型进行匹配)  
@Resource属于 JDK 提供的注解，默认注入方式为 byName。如果无法通过名称匹配到对应的 Bean 的话，注入方式会变为byType。

#### Bean 的作用域有哪些
使用`@Scope()`可配置bean的作用域
- singleton : IoC 容器中只有唯一的 bean 实例。Spring 中的 bean 默认都是单例的，是对单例设计模式的应用。
- prototype : 每次获取都会创建一个新的 bean 实例。也就是说，连续 getBean() 两次，得到的是不同的 Bean 实例。
- request （仅 Web 应用可用）: 每一次 HTTP 请求都会产生一个新的 bean（请求 bean），该 bean 仅在当前 HTTP request 内有效。
- session （仅 Web 应用可用） : 每一次来自新 session 的 HTTP 请求都会产生一个新的 bean（会话 bean），该 bean 仅在当前 HTTP session 内有效。
- application/global-session （仅 Web 应用可用）： 每个 Web 应用在启动时创建一个 Bean（应用 Bean），，该 bean 仅在当前应用启动时间内有效。
- websocket （仅 Web 应用可用）：每一次 WebSocket 会话产生一个新的 bean。

### Spring AOP
AOP(Aspect-Oriented Programming:面向切面编程)能够将那些与业务无关，却为业务模块所共同调用的逻辑或责任（例如事务处理、日志管理、权限控制等）封装起来，便于减少系统的重复代码，降低模块间的耦合度，并有利于未来的可拓展性和可维护性。

Spring AOP 就是基于动态代理的，如果要代理的对象，实现了某个接口，那么 Spring AOP 会使用 JDK Proxy，去创建代理对象，而对于没有实现接口的对象，就无法使用 JDK Proxy 去进行代理了，这时候 Spring AOP 会使用 **Cglib** 生成一个被代理对象的子类来作为代理

#### Spring AOP 和 AspectJ AOP 有什么区别
Spring AOP 属于运行时增强，而 AspectJ 是编译时增强。 Spring AOP 基于代理(Proxying)，而 AspectJ 基于字节码操作(Bytecode Manipulation)。

#### AspectJ 定义的通知类型有哪些
- Before（前置通知）：目标对象的方法调用之前触发
- After （后置通知）：目标对象的方法调用之后触发
- AfterReturning（返回通知）：目标对象的方法调用完成，在返回结果值之后触发
- AfterThrowing（异常通知） ：目标对象的方法运行中抛出 / 触发异常后触发。AfterReturning 和 AfterThrowing 两者互斥。如果方法调用成功无异常，则会有返回值；如果方法抛出了异常，则不会有返回值。
- Around： （环绕通知）编程式控制目标对象的方法调用。环绕通知是所有通知类型中可操作范围最大的一种，因为它可以直接拿到目标对象，以及要执行的方法，所以环绕通知可以任意的在目标对象的方法调用前后搞事，甚至不调用目标对象的方法

#### 多个切面的执行顺序如何控制
使用 @Order 注解，或者使用 Ordered 接口重写 getOrder() 方法

### Spring MVC
MVC 是模型(Model)、视图(View)、控制器(Controller)的简写，其核心思想是通过将业务逻辑、数据、显示分离来组织代码。

#### Spring MVC 的核心组件有哪些
- DispatcherServlet ：核心的中央处理器，负责接收请求、分发，并给予客户端响应。
- HandlerMapping ：处理器映射器，根据 uri 去匹配查找能处理的 Handler ，并会将请求涉及到的拦截器和 Handler 一起封装。
- HandlerAdapter ：处理器适配器，根据 HandlerMapping 找到的 Handler ，适配执行对应的 Handler；
- Handler ：请求处理器，处理实际请求的处理器。
- ViewResolver ：视图解析器，根据 Handler 返回的逻辑视图 / 视图，解析并渲染真正的视图，并传递给 DispatcherServlet 响应客户端


#### Spring、SpringMVC与Springboot的区别
`Spring` 是一个综合性的开发框架，提供了大量的功能和模块，用于开发各种类型的应用程序，包括Web应用、企业级应用、批处理应用等。Spring框架采用模块化的设计，包含了核心容器、数据访问、Web开发、AOP、消息队列等模块。使用Spring框架，开发人员需要手动进行配置和设置，定义各个模块的组件和关系。

`Spring Boot` 是一个用于快速构建独立的、可扩展的、基于Spring的应用程序的框架。它旨在简化Spring应用程序的配置和部署过程。Spring Boot提供了自动配置的特性，通过约定大于配置的方式，大大减少了开发人员需要编写的配置代码量。它内置了一个嵌入式Servlet容器（如Tomcat、Jetty等），可以独立运行应用程序，而无需外部的Java EE容器。

`Spring MVC` 是基于Spring框架的一个Web应用程序开发框架，它提供了一套用于构建Web应用程序的组件和模型。Spring MVC需要进行配置和设置，包括定义控制器、处理请求映射、配置视图解析器等。它是基于Servlet技术的，需要依赖于Java EE容器（如Tomcat）来运行。

综上所述，`Spring`是一个综合性的开发框架，提供了各种模块和功能，需要手动配置和设置。  
`Spring MVC`是一个传统的基于Spring的Web应用程序开发框架，需要进行详细的配置和设置。  
`Spring Boot`是Spring的子项目，旨在简化配置和部署过程，提供了自动配置和开箱即用的特性，使得开发人员能够更加快速地搭建和开发应用程序。可以说，Spring Boot是在Spring框架的基础上提供了更高层次的抽象和简化。

#### Springboot的自动配置
重点在`@EnableAutoConfiguration` 注解，约定大于配置，@EnableAutoConfiguration注解可以自动载入应用程序所需要的所有默认配置

#### SpringBoot 中的 starter
starter提供了一个自动化配置类，一般命名为 XXXAutoConfiguration ，在这个配置类中通过条件注解来决定一个配置是否生效（条件注解就是 Spring 中原本就有的），然后它还会提供一系列的默认配置，也允许开发者根据实际情况自定义相关配置，然后通过类型安全的属性注入将这些配置属性注入进来，新注入的属性会代替掉默认属性。正因为如此，很多第三方框架，我们只需要引入依赖就可以直接使用了。

#### Spring Boot 的核心注解是哪个
启动类上面的注解`@SpringBootApplication`，它也是 Spring Boot 的核心注解，主要组合包含了以下 3 个注解：
- @SpringBootConfiguration：组合了 @Configuration 注解，实现配置文件的功能。
- @EnableAutoConfiguration：打开自动配置的功能，也可以关闭某个自动配置的选项，如关闭数据源自动配置功能： @SpringBootApplication(exclude = { DataSourceAutoConfiguration.class })。
- @ComponentScan：Spring组件扫描。

### Spring事务
#### Spring管理事务的方式有几种
- 编程式事务 ： 在代码中硬编码(不推荐使用) : 通过 `TransactionTemplate`或者 `TransactionManager` 手动管理事务，实际应用中很少使用，但是对于你理解 Spring 事务管理原理有帮助。
- 声明式事务 ： 在 XML 配置文件中配置或者直接基于注解（推荐使用） : 实际是通过 AOP 实现（基于`@Transactional` 的全注解方式使用最多）
PS: @Transactional默认只在RuntimeException回滚，通常需要手动指定`@Transactional(rollbackFor = Exception.class)`

#### Spring事务有哪几种传播行为
当事务方法被另一个事务方法调用时，需要指定事务应该如何传播

1.TransactionDefinition.PROPAGATION_REQUIRED  
如果当前存在事务，则加入该事务；如果当前没有事务，则创建一个新的事务。  
使用的最多的一个事务传播行为，我们平时经常使用的`@Transactional`注解默认使用就是这个事务传播行为。

2.TransactionDefinition.PROPAGATION_REQUIRES_NEW  
创建一个新的事务，如果当前存在事务，则把当前事务挂起。  
也就是说不管外部方法是否开启事务，Propagation.REQUIRES_NEW修饰的内部方法会新开启自己的事务，且开启的事务相互独立，互不干扰。

3.TransactionDefinition.PROPAGATION_NESTED  
如果当前存在事务，则创建一个事务作为当前事务的嵌套事务来运行；如果当前没有事务，则该取值等价于TransactionDefinition.PROPAGATION_REQUIRED。

4.TransactionDefinition.PROPAGATION_MANDATORY  
如果当前存在事务，则加入该事务；如果当前没有事务，则抛出异常。（mandatory：强制性）

**以下3种传播方式会导致事务失效**

5.TransactionDefinition.PROPAGATION_SUPPORTS  
如果当前存在事务，则加入该事务；如果当前没有事务，则以非事务的方式继续运行。

6.TransactionDefinition.PROPAGATION_NOT_SUPPORTED  
以非事务方式运行，如果当前存在事务，则把当前事务挂起。

7.TransactionDefinition.PROPAGATION_NEVER  
以非事务方式运行，如果当前存在事务，则抛出异常。

#### Spring事务的隔离级别有哪几种
TransactionDefinition.ISOLATION_DEFAULT  
使用后端数据库默认的隔离级别，MySQL 默认采用的 REPEATABLE_READ 隔离级别 Oracle 默认采用的 READ_COMMITTED 隔离级别.

TransactionDefinition.ISOLATION_READ_UNCOMMITTED  
最低的隔离级别，使用这个隔离级别很少，因为它允许读取尚未提交的数据变更，可能会导致脏读、幻读或不可重复读

TransactionDefinition.ISOLATION_READ_COMMITTED  
允许读取并发事务已经提交的数据，可以阻止脏读，但是幻读或不可重复读仍有可能发生

TransactionDefinition.ISOLATION_REPEATABLE_READ  
对同一字段的多次读取结果都是一致的，除非数据是被本身事务自己所修改，可以阻止脏读和不可重复读，但幻读仍有可能发生。

TransactionDefinition.ISOLATION_SERIALIZABLE  
最高的隔离级别，完全服从 ACID 的隔离级别。所有的事务依次逐个执行，这样事务之间就完全不可能产生干扰，也就是说，该级别可以防止脏读、不可重复读以及幻读。但是这将严重影响程序的性能。通常情况下也不会用到该级别。

#### Spring事务失效的几种情况
- **方法不是`public`修饰的**。Spring要求被代理的方法必须是public的
- **方法被`final`修饰**。Spring事务底层使用了aop，也就是通过`jdk动态代理`或者`cglib`生成代理类，如果方法被final修饰了，在代理类里就无法重写该方法，也无法添加事务功能。被`static`修饰的方法也一样会失效。
- **在类的内部被调用**。例如在add方法中调用this.update，这样不会经过Spring的代理，也就无法被Spring添加事务。解决方法是使用 `TransactionTemplate`或者 `TransactionManager` 手动添加事务
- **业务与事务不在同一线程中**。Spring事务实现中使用了ThreadLocal，ThreadLocal只能在同一个线程中生效，例如在方法中使用了另一个线程操作数据库则事务不会生效
- **异常类型不匹配**。`@Transactional`默认只在遇到RuntimeException时回滚，如果要所有都回滚则需要配置`@Transactional(rollbackFor=Exception.class)`
- **异常被方法内部处理**。异常被方法内部处理而没有抛出异常，则不会进行回滚
- **数据源未配置事务管理器**。
- **数据库引擎不支持事务**。MySQL5.5之前的默认存储引擎是**MyISAM**，这个引擎不支持事务，**Innodb**才支持
- **事务传播行为设置错误**。PROPAGATION_SUPPORTS，PROPAGATION_NOT_SUPPORTED，PROPAGATION_NEVER 会导致方法运行在非事务状态下，从而导致事务失效
- 类未被Spring管理，例如未注册为Bean(没添加相应注解 @Controller,@Service,@Component,@Bean)
