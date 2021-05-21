---
title: OAuth2.0资源服务器搭建与配置
date: 2020-04-11 22:52:26
tags: [OAuth]
categories: [OAuth]
---

# OAuth2.0资源服务器搭建
## 引入依赖
```xml
<dependency>
  <groupId>org.springframework.cloud</groupId>
  <artifactId>spring-cloud-starter-security</artifactId>
</dependency>
<dependency>
  <groupId>org.springframework.cloud</groupId>
  <artifactId>spring-cloud-starter-oauth2</artifactId>
</dependency>
```

## 编写配置文件
`SecurityConfig.java`
```java
@Configuration
@EnableWebSecurity
@EnableGlobalMethodSecurity(prePostEnabled = true)
public class SecurityConfig extends WebSecurityConfigurerAdapter {
    @Override
    protected void configure(HttpSecurity http) throws Exception {
        http.authorizeRequests()
            .antMatchers("/res/**")
            // 需要通过验证
            .authenticated()
            .anyRequest().permitAll()
            .and().csrf().disable();
    }
}
```

<!-- more -->

`ResourceServerConfig.java`
```java
@Configuration
@EnableResourceServer
public class ResourceServerConfig extends ResourceServerConfigurerAdapter {
    @Autowired
    private TokenStore tokenStore;

    @Override
    public void configure(ResourceServerSecurityConfigurer resources) throws Exception {
        resources.resourceId("res1")  // 资源id, 需要与ClientDetailsServiceConfigurer中的resourceIds一致
                .tokenServices(tokenService()) // 验证令牌服务
                .tokenStore(tokenStore)
                .stateless(true);
    }

    @Override
    public void configure(HttpSecurity http) throws Exception {
        http.authorizeRequests()
                .antMatchers("/**")
                // 校验scope
                .access("#oauth2.hasScope('all')")
            .and().csrf().disable()
                .sessionManagement().sessionCreationPolicy(SessionCreationPolicy.STATELESS);
    }

    @Bean
    public ResourceServerTokenServices tokenService() {
        // 远程服务器校验token。调用授权服务器校验token
        RemoteTokenServices services = new RemoteTokenServices();
        // 使用远程服务校验时，必须指定endpoint_url, client_id, client_secret
        services.setCheckTokenEndpointUrl("http://127.0.0.1:80/oauth/check_token");
        services.setClientId("c1");
        services.setClientSecret("secret");
        return services;
    }
}
```

控制器
```java
@RestController
public class IndexController {
    @RequestMapping("/res/r1")
    @PreAuthorize("hasRole('USER')")
    public String resource01() {
        return "访问资源r1";
    }
}
```
