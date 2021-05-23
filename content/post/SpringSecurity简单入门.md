---
title: 'SpringSecurity简单入门'
date: 2020-03-23 04:35:18
tags: [Java,SpringSecurity]
categories: [编程,SpringSecurity]
---

# Spring Security 是什么
`Spring Security`是一套**认证授权**框架，支持认证模式如HTTP BASIC 认证头 (基于 IETF RFC-based 标准)，HTTP Digest 认证头 ( IETF RFC-based 标准)，Form-based authentication (用于简单的用户界面)，OpenID 认证等，Spring Security使得当前系统可以快速集成这些验证机制亦或是实现自己的一套验证机制。

`Spring Security`可以对所以进入系统的请求进行拦截，效验每个请求是否能访问对应的资源。

# Spring Security 如何工作
`Spring Security`对Web 资源的保护是靠`Filter`来实现的，当初始化`Spring Security`时，会创建一个名为`SpringSecurityFilterChain`的过滤器，类型为`org.springframework.security.web.FilterChainProxy`，它实现了`javax.servlet.Filter`，因此外部的请求都会经过这个过滤器。

`SpringSecurityFilterChain`中包含的各个`Filter`都作为`Bean`被`Spring`所管理，他们是`Spring Security`的核心，各司其职，但这些`Filter`并不直接处理用户的认证，也不直接处理用户的授权，而出把这些工作交给**认证管理器**(AuthenticationManager)和**决策管理器**(AccessDecisionManager)进行处理。

# 认证流程
![SpringSecurity认证流程](https://s1.ax1x.com/2020/03/28/GFBovV.png)

<!-- more -->

# 引入依赖
在`pom.xml`中写入以下依赖
```xml
<dependency>
  <groupId>org.springframework.boot</groupId>
  <artifactId>spring-boot-starter-security</artifactId>
</dependency>
<dependency>
  <groupId>org.springframework.security</groupId>
  <artifactId>spring-security-test</artifactId>
  <scope>test</scope>
</dependency>
```

# 编写配置
```java
package com.ilirus.oauth.config;

import org.springframework.security.config.annotation.authentication.builders.AuthenticationManagerBuilder;
import org.springframework.security.config.annotation.web.builders.HttpSecurity;
import org.springframework.security.config.annotation.web.configuration.EnableWebSecurity;
import org.springframework.security.config.annotation.web.configuration.WebSecurityConfigurerAdapter;
import org.springframework.security.crypto.bcrypt.BCryptPasswordEncoder;

@Configuration
@EnableWebSecurity
public class SecurityConfig extends WebSecurityConfigurerAdapter {
    @Override
    protected void configure(AuthenticationManagerBuilder auth) throws Exception {
        // 在内存中创建用户
        auth.inMemoryAuthentication()
            // 使用BCryptPasswordEncoder 加密密码
              .passwordEncoder(new BCryptPasswordEncoder())
            // 用户名为 user
              .withUser("user")
            // 密码为 123
              .password(new BCryptPasswordEncoder().encode("123"))
            //拥有 USER 权限
              .roles("USER");
    }
    @Override
    protected void configure(HttpSecurity http) throws Exception {
        http.authorizeRequests()
            // 匹配"/","/index","/error"
            .antMatchers("/","/index","/error")
            // 不需要权限即可访问
            .permitAll()
            // 匹配"/user" 路径下的所有
            .antMatchers("/user/**")
            // 拥有USER 权限才可访问
            .hasRole("USER")
            .antMatchers("/admin/**")
            .hasRole("ADMIN")
            .and()
            // 允许使用表单登录
            .formLogin()
            // 自定义登录页面地址
            .loginPage("/login")
            // 指定处理登录的url, 即登录表单的action对应的地址
            .loginProcessingUrl("/login/identify")
            // 成功后默认跳转到"/user"
            .defaultSuccessUrl("/user")
            .and().logout()
            // 自定义注销地址
            .logoutUrl("/logout")
            // 注销后跳转到 "/login"
            .logoutSuccessUrl("/login")
            // 注销时是否让HttpSession 无效化
            .invalidateHttpSession(true)
            .and()
            // 禁用跨域请求验证
            .csrf().disable();
    }
}
```

如果不想关闭`CSRF`保护，则需要在自定义的登录表单中添加如下代码
```html
<input type="hidden" th:if="${_csrf != null}" th:name="${_csrf.parameterName}" th:value="${_csrf.token}" />
```

至此，一个最简单的SpringSecurity 验证就引入到了demo 中

## 使用`UserDetailsManager`创建用户:
```java
@Bean
public UserDetailsService userDetailsService() {
    InMemoryUserDetailsManager manager = new InMemoryUserDetailsManager();
    manager.createUser(User
            .withUsername("user")
            .password("123")
            .roles("USER")
            .build());

    return manager;
}
// 密码编码器
@Bean
public PasswordEncoder passwordEncoder() {
    // NoOpPasswordEncoder: 不进行编码
    return NoOpPasswordEncoder.getInstance();
}
```

# 连接数据库
修改之前的配置类
```java
@Configuration
@EnableWebSecurity
public class SecurityConfig extends WebSecurityConfigurerAdapter {
    @Bean
    public PasswordEncoder passwordEncoder() {
        // 密码编码器
        return new BCryptPasswordEncoder();
    }

    @Override
    protected void configure(HttpSecurity http) throws Exception {
        http.authorizeRequests()
            .antMatchers("/","/index","/error").permitAll()
            .antMatchers("/user/**").hasRole("USER")
            .and().formLogin();
    }
}
```

自定义一个service，继承`UserDetailsService`类
```java
package com.ilirus.oauth.service;

import com.ilirus.oauth.dao.UserDao;
import com.ilirus.oauth.entities.UserEntity;
import org.springframework.security.core.userdetails.User;
import lombok.extern.slf4j.Slf4j;
import org.springframework.security.core.userdetails.UserDetails;
import org.springframework.security.core.userdetails.UserDetailsService;
import org.springframework.security.core.userdetails.UsernameNotFoundException;
import org.springframework.stereotype.Service;

import javax.annotation.Resource;

@Service
@Slf4j
public class UserService implements UserDetailsService {
    @Resource
    private UserDao dao;

    @Override
    public UserDetails loadUserByUsername(String username) throws UsernameNotFoundException {
        log.info("Username: "+username);
        // 数据库查询
        UserEntity user = dao.getUser(username);
        UserDetails userDetails = User.withUsername(user.getName())
                                      .password(user.getPassword())
                                      .roles("USER").build();
        return userDetails;
    }
}
```

`loadUserByUsername`方法中的参数即为登录表单中的用户名，在数据库中查询到对应用户的信息后，将其装入`UserDetails`中，然后返回给`DaoAuthenticationProvider`。

`DaoAuthenticationProvider`会使用`PasswordEncoder`对用户提交的表单中的密码进行编码，然后将其与从`UserDetailService`中接收到的UserDetail 中的密码信息进行对比，以判断是否通过。
