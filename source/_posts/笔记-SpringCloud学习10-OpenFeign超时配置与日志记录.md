---
title: '[笔记]SpringCloud学习10: OpenFeign超时配置与日志记录'
date: 2020-03-29 22:50:00
tags: [Java,SpringCloud,OpenFeign]
categories: [学习笔记,SpringCloud]
---

# 设置超时时间
在`application.yml`中
```yml
# 设置超时时间
ribbon:
  # 指与服务器建立链接的超时时间(单位: ms)
  ConnectTimeout: 1000
  # 指与服务器建立链接之后，从服务器读取到可用资源所花时间
  ReadTimeout: 5000
```

# 配置日志输出
`OpenFeign`有4个日志级别
- `NONE`: 不输出任何日志
- `BASIC`: 仅记录请求方法、URL、响应状态吗以及执行时间
- `HEADERS`: 除了`BASIC`中的信息外，还记录请求和响应的头信息
- `FULL`: 输出所有信息

编写配置类
```java
package com.ilirus.springcloud.config;

import feign.Logger;
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;

@Configuration
public class OpenFeignConfig {
    @Bean
    Logger.Level feignLoggerLevel() {
        return Logger.Level.FULL;
    }
}
```

在`application.yml`中配置控制台日志输出级别
```yml
logging:
  level:
    com.ilirus.springcloud.service.PaymentFeignService: debug
```