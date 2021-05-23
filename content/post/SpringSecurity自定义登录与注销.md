---
title: SpringSecurity自定义登录与注销
date: 2020-03-31 22:34:17
tags: [Java,SpringSecurity]
categories: [编程,SpringSecurity]
---

# 自定义登录页
修改配置
```java
@Configuration
@EnableWebSecurity
public class SecurityConfig extends WebSecurityConfigurerAdapter {
    @Override
    protected void configure(HttpSecurity http) throws Exception {
        http.authorizeRequests()
            .antMatchers("/","/index","/error","/identify").permitAll()
            .antMatchers("/user/**").hasRole("USER")
            .and().formLogin()
            // 自定义登录页面地址
            .loginPage("/login")
            // 指定处理登录请求的url, 即登录表单的action对应的地址
            .loginProcessingUrl("/identify/login")
            // 成功后默认跳转到的url
            .defaultSuccessUrl("/user")
            .and()
            // 禁用跨域请求验证
            .csrf().disable();
    }
}
```

映射路径
```java
// RequestMapping 的路径, 即为配置类中loginPage设置的路径
@RequestMapping("/login")
public String login() {
    return "login";
}
```

页面编写
```html
<!DOCTYPE html>
<html lang="en" xmlns:th="http://www.thymeleaf.org">
<head>
  <meta charset="UTF-8">
  <title>Login</title>
</head>
<body>
  <!-- action中的url即为配置类中, loginProcessingUrl项设置的url -->
  <!-- 登录请求一律用post发送, 不能用get -->
  <form method="post" action="/identify/login">
    <h2>Please sign in</h2>
    <div>
      <label for="username">Username</label>
      <input type="text" id="username" name="username" placeholder="Username" required autofocus>
    </div>
    <div>
      <label for="password">Password</label>
      <input type="password" id="password" name="password" placeholder="Password" required>
    </div>
    <!-- 如果没有在配置类中关闭csrf验证, 则需要在表单中加入下面的表单项 -->
    <input type="hidden" th:if="${_csrf != null}" th:name="${_csrf.parameterName}" th:value="${_csrf.token}" />
    <button type="submit">Sign in</button>
  </form>
</body>
</html>
```

<!-- more -->

# 自定义注销

修改配置
```java
@Configuration
@EnableWebSecurity
public class SecurityConfig extends WebSecurityConfigurerAdapter {
    @Override
    protected void configure(HttpSecurity http) throws Exception {
        http.authorizeRequests()
            .antMatchers("/","/index","/error","/identify").permitAll()
            .antMatchers("/user/**").hasRole("USER")
            .and().formLogin()
            .loginPage("/login")
            .loginProcessingUrl("/identify/login")
            .and().logout()
            // 指定处理注销请求的url, 即注销表单的action对应的地址
            .logoutUrl("/identify/logout")
            // 指定注销后的跳转页面
            .logoutSuccessUrl("/index")
            // 注销时是否一并也让HttpSession 无效化
            .invalidateHttpSession(true)
            .and().csrf().disable();
    }
}
```

映射路径
```java
@RequestMapping("/logout")
public String logout() {
    return "logout";
}
```

页面编写
```html
<!DOCTYPE html>
<html lang="en" xmlns:th="http://www.thymeleaf.org">
<head>
  <meta charset="UTF-8">
  <title>Logout</title>
</head>
<body>
  <form method="post" action="/identify/logout">
    <h2>Are you sure?</h2>
    <!-- 如果没有在配置类中关闭csrf验证, 则需要在表单中加入下面的表单项 -->
    <input type="hidden" th:if="${_csrf != null}" th:name="${_csrf.parameterName}" th:value="${_csrf.token}" />
    <button type="submit">Yes, logout</button>
  </form>
  <!-- 如果关闭了csrf验证, 则注销请求可用get发送 -->
  <a href="/identify/logout">Logout</a>
</body>
</html>
```
