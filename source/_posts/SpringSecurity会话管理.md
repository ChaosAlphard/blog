---
title: SpringSecurity会话管理
date: 2020-04-03 01:00:28
tags: [Java, SpringSecurity]
categories: [SpringSecurity]
---

# 在Controller中获取认证信息
```java
@Controller
public class IndexController {
    @RequestMapping("/user")
    public String user(Model model) {
        String username = getUserName();
        model.addAttribute("username",username);
        return "user";
    }

    private String getUserName() {
        // 获取认证信息
        Authentication authentication = SecurityContextHolder.getContext().getAuthentication();

        // 检查用户是否认证
        if(authentication == null || !authentication.isAuthenticated()) {
            return "游客";
        }

        Object principal = authentication.getPrincipal();
        if(principal instanceof UserDetails) {
            return ((UserDetails) principal).getUsername();
        } else {
            return principal.toString();
        }
    }
}
```

# 会话控制
在配置类中通过下列选项控制会话何时创建以及Spring Security 如何与之交互
- `always`: 如果没有session, 则创建一个
- `ifRequired`: 如果需要, 则创建一个. (默认)
- `never`: Spring Security 不会主动创建session 但如果应用中其他地方创建了session 那么Spring Security 将会使用session
- `stateless`: Spring Security 将不会创建session, 也不会使用session

```java
@Configuration
@EnableWebSecurity
public class SecurityConfig extends WebSecurityConfigurerAdapter {
    @Override
    protected void configure(HttpSecurity http) throws Exception {
        http.sessionManagement()
            .sessionCreationPolicy(SessionCreationPolicy.IF_REQUIRED);
    }
```