---
title: '[笔记]SpringCloud学习01: 项目的搭建'
date: 2020-03-12 00:55:33
tags: [Java,SpringCloud]
categories: [编程,微服务,SpringCloud]
---

# 建立项目
## 父工程的创建
IDEA: Create New Project -> Maven -> 输入groupid和artifactId -> 完成

之后在IDEA设置中还有几个地方要设置:
- Build选项中的Compiler选项中的`Annotation Processing`目录中，在`Enable annotation processing`选项前打勾
- Build选项中的Compiler选项中的`Java Compiler`目录中，将`Target bytecode version` 选为`8`
- (可选)Editor选项中的`File Types`目录中，将`*.iml;*.idea;` 添加到`Ignore files and patterns`中，这是用于在IDEA资源管理器中隐藏对应后缀的文件

修改`pom.xml`文件，在`version`下添加`<packaging>pom</packaging>`
```xml
  <groupId>com.ilirus.springcloud</groupId>
  <artifactId>irscloud</artifactId>
  <version>1.0-SNAPSHOT</version>
  <packaging>pom</packaging>
```

<!-- more -->

修改`properties`
```xml
<properties>
  <project.build.sourceEncoding>UTF-8</project.build.sourceEncoding>
  <maven.compiler.source>1.8</maven.compiler.source>
  <maven.compiler.target>1.8</maven.compiler.target>
  <junit.version>4.12</junit.version>
  <log4j.version>1.2.17</log4j.version>
  <lombok.version>1.16.18</lombok.version>
  <mysql.version>8.0.14</mysql.version>
  <druid.version>1.1.16</druid.version>
  <mybatis.spring.boot.version>2.1.2</mybatis.spring.boot.version>
</properties>
```

修改`dependencies`
```xml
<dependencyManagement>
  <dependencies>
    <!-- springboot 2.2.2 -->
    <dependency>
      <groupId>org.springframework.boot</groupId>
      <artifactId>spring-boot-dependencies</artifactId>
      <version>2.2.2.RELEASE</version>
      <type>pom</type>
      <scope>import</scope>
    </dependency>
    <!-- spring cloud hoxton.SR1 -->
    <dependency>
      <groupId>org.springframework.cloud</groupId>
      <artifactId>spring-cloud-dependencies</artifactId>
      <version>Hoxton.SR1</version>
      <type>pom</type>
      <scope>import</scope>
    </dependency>
    <!-- spring cloud alibaba 2.1.0.RELEASE -->
    <dependency>
      <groupId>com.alibaba.cloud</groupId>
      <artifactId>spring-cloud-alibaba-dependencies</artifactId>
      <version>2.1.0.RELEASE</version>
      <type>pom</type>
      <scope>import</scope>
    </dependency>
    <!-- mysql -->
    <dependency>
      <groupId>mysql</groupId>
      <artifactId>mysql-connector-java</artifactId>
      <version>${mysql.version}</version>
    </dependency>
    <!-- alibaba -->
    <dependency>
      <groupId>com.alibaba</groupId>
      <artifactId>druid</artifactId>
      <version>${druid.version}</version>
    </dependency>
    <!-- mybatis -->
    <dependency>
      <groupId>org.mybatis.spring.boot</groupId>
      <artifactId>mybatis-spring-boot-starter</artifactId>
      <version>${mybatis.spring.boot.version}</version>
    </dependency>
    <!-- junit -->
    <dependency>
      <groupId>junit</groupId>
      <artifactId>junit</artifactId>
      <version>${junit.version}</version>
    </dependency>
    <!-- log4j -->
    <dependency>
      <groupId>log4j</groupId>
      <artifactId>log4j</artifactId>
      <version>${log4j.version}</version>
    </dependency>
    <!-- lombok -->
    <dependency>
      <groupId>org.projectlombok</groupId>
      <artifactId>lombok</artifactId>
      <version>${lombok.version}</version>
      <optional>true</optional>
    </dependency>
  </dependencies>
</dependencyManagement>
```

修改`build`
```xml
<build>
  <plugins>
    <plugin>
      <groupId>org.springframework.boot</groupId>
      <artifactId>spring-boot-maven-plugin</artifactId>
      <configuration>
        <fork>true</fork>
        <addResources>true</addResources>
      </configuration>
    </plugin>
  </plugins>
</build>
```

最终`pom.xml`
<details><summary>点击展开</summary>```xml
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
  <modelVersion>4.0.0</modelVersion>

  <groupId>com.ilirus.springcloud</groupId>
  <artifactId>irscloud</artifactId>
  <version>1.0-SNAPSHOT</version>
  <packaging>pom</packaging>

  <!-- 统一版本管理 -->
  <properties>
    <project.build.sourceEncoding>UTF-8</project.build.sourceEncoding>
    <maven.compiler.source>1.8</maven.compiler.source>
    <maven.compiler.target>1.8</maven.compiler.target>
    <junit.version>4.12</junit.version>
    <log4j.version>1.2.17</log4j.version>
    <lombok.version>1.16.18</lombok.version>
    <mysql.version>8.0.14</mysql.version>
    <druid.version>1.1.16</druid.version>
    <mybatis.spring.boot.version>2.1.2</mybatis.spring.boot.version>
  </properties>

  <!-- 子项目统一依赖版本管理，子项目若不声明则不会自动引入 -->
  <!-- 子项目可只声明groupId 和artifactId, 版本会从这里继承 -->
  <dependencyManagement>
    <dependencies>
      <!-- springboot 2.2.2 -->
      <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-dependencies</artifactId>
        <version>2.2.2.RELEASE</version>
        <type>pom</type>
        <scope>import</scope>
      </dependency>
      <!-- spring cloud hoxton.SR1 -->
      <dependency>
        <groupId>org.springframework.cloud</groupId>
        <artifactId>spring-cloud-dependencies</artifactId>
        <version>Hoxton.SR1</version>
        <type>pom</type>
        <scope>import</scope>
      </dependency>
      <!-- spring cloud alibaba 2.1.0.RELEASE -->
      <dependency>
        <groupId>com.alibaba.cloud</groupId>
        <artifactId>spring-cloud-alibaba-dependencies</artifactId>
        <version>2.1.0.RELEASE</version>
        <type>pom</type>
        <scope>import</scope>
      </dependency>
      <!-- mysql -->
      <dependency>
        <groupId>mysql</groupId>
        <artifactId>mysql-connector-java</artifactId>
        <version>${mysql.version}</version>
      </dependency>
      <!-- alibaba -->
      <dependency>
        <groupId>com.alibaba</groupId>
        <artifactId>druid</artifactId>
        <version>${druid.version}</version>
      </dependency>
      <!-- mybatis -->
      <dependency>
        <groupId>org.mybatis.spring.boot</groupId>
        <artifactId>mybatis-spring-boot-starter</artifactId>
        <version>${mybatis.spring.boot.version}</version>
      </dependency>
      <!-- junit -->
      <dependency>
        <groupId>junit</groupId>
        <artifactId>junit</artifactId>
        <version>${junit.version}</version>
      </dependency>
      <!-- log4j -->
      <dependency>
        <groupId>log4j</groupId>
        <artifactId>log4j</artifactId>
        <version>${log4j.version}</version>
      </dependency>
      <!-- lombok -->
      <dependency>
        <groupId>org.projectlombok</groupId>
        <artifactId>lombok</artifactId>
        <version>${lombok.version}</version>
        <optional>true</optional>
      </dependency>
    </dependencies>
  </dependencyManagement>

  <build>
    <plugins>
      <plugin>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-maven-plugin</artifactId>
        <configuration>
          <fork>true</fork>
          <addResources>true</addResources>
        </configuration>
      </plugin>
    </plugins>
  </build>
</project>
```
</details>

之后在`Maven Project`窗口中的Lifcycle下执行`install`选项以检查是否正确

## 子工程的创建
### 创建子工程module
在`Project`窗口中右键点击父工程，在弹出的菜单中选择New -> Module -> 在弹出的窗口中选择Maven -> 确认`Add as module to`和`Parent`皆为父工程，并输入artifactId -> 确认`Nodule name`(IDEA会自动去掉某些特殊符号) -> 完成

返回`父工程`的`pom.xml`中，查看是否有`modules`元素
```xml
<modules>
  <module>child-project</module>
</modules>
```

### 配置子工程依赖
向`子工程`的`pom.xml`添加依赖
```xml
<dependencies>
  <dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-web</artifactId>
  </dependency>
  <dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-actuator</artifactId>
  </dependency>
  <dependency>
    <groupId>org.mybatis.spring.boot</groupId>
    <artifactId>mybatis-spring-boot-starter</artifactId>
  </dependency>
  <dependency>
    <groupId>com.alibaba</groupId>
    <artifactId>druid-spring-boot-starter</artifactId>
    <version>1.1.10</version>
  </dependency>
  <dependency>
    <groupId>mysql</groupId>
    <artifactId>mysql-connector-java</artifactId>
  </dependency>
  <dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-jdbc</artifactId>
  </dependency>
  <dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-devtools</artifactId>
  </dependency>
  <dependency>
    <groupId>org.projectlombok</groupId>
    <artifactId>lombok</artifactId>
    <optional>true</optional>
  </dependency>
  <dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-test</artifactId>
    <scope>test</scope>
  </dependency>
</dependencies>
```

最终`pom.xml`
<details><summary>点击展开</summary>```xml
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
  <parent>
    <groupId>com.ilirus.springcloud</groupId>
    <artifactId>irscloud</artifactId>
    <version>1.0-SNAPSHOT</version>
  </parent>
  <modelVersion>4.0.0</modelVersion>

  <artifactId>irs-provider-payment8001</artifactId>

  <dependencies>
    <dependency>
      <groupId>org.springframework.boot</groupId>
      <artifactId>spring-boot-starter-web</artifactId>
    </dependency>
    <dependency>
      <groupId>org.springframework.boot</groupId>
      <artifactId>spring-boot-starter-actuator</artifactId>
    </dependency>
    <dependency>
      <groupId>com.alibaba</groupId>
      <artifactId>druid-spring-boot-starter</artifactId>
      <version>1.1.10</version>
    </dependency>
    <dependency>
      <groupId>org.mybatis.spring.boot</groupId>
      <artifactId>mybatis-spring-boot-starter</artifactId>
    </dependency>
    <dependency>
      <groupId>mysql</groupId>
      <artifactId>mysql-connector-java</artifactId>
    </dependency>
    <dependency>
      <groupId>org.springframework.boot</groupId>
      <artifactId>spring-boot-starter-jdbc</artifactId>
    </dependency>
    <dependency>
      <groupId>org.springframework.boot</groupId>
      <artifactId>spring-boot-devtools</artifactId>
    </dependency>
    <dependency>
      <groupId>org.projectlombok</groupId>
      <artifactId>lombok</artifactId>
      <optional>true</optional>
    </dependency>
    <dependency>
      <groupId>org.springframework.boot</groupId>
      <artifactId>spring-boot-starter-test</artifactId>
      <scope>test</scope>
    </dependency>
  </dependencies>
</project>
```
</details>

### 编写application.yml
在子工程的`src/main/resources`目录下创建`application.yml`文件
<details><summary>点击展开</summary>```yml
server:
  port: 8001 # 端口

spring:
  application:
    name: child-project # 名称
  datasource:
    type: com.alibaba.druid.pool.DruidDataSource # 数据源类型
    driver-class-name: com.mysql.cj.jdbc.Driver     # 数据库驱动包
    url: jdbc:mysql://127.0.0.1:3306/irscloud?useSSL=false&characterEncoding=utf8&useUnicode=true
    username: ...
    password: ...

mybatis:
  mapper-locations: classpath:mapper/*.xml
  type-aliases-package: com.ilirus.springcloud.entities # entity别名类所在的package
```
</details>

### 创建启动类
在子工程的`src/main/java/<groupId>`目录下创建对应的启动类
<details><summary>点击展开</summary>```java
package com.ilirus.springcloud;

import org.mybatis.spring.annotation.MapperScan;
import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;

@SpringBootApplication
@MapperScan("com.ilirus.springcloud.dao")
public class ChildProjectMain {
    public static void main(String[] args) {
        SpringApplication.run(ChildProject.class, args);
    }
}
```
</details>

### 编写实体类
<details><summary>点击展开</summary>```java
package com.ilirus.springcloud.entities;

import lombok.AllArgsConstructor;
import lombok.Data;
import lombok.NoArgsConstructor;
import java.io.Serializable;

@Data
@AllArgsConstructor
@NoArgsConstructor
public class Payment implements Serializable {
    private Long id;
    private String serial;
}
```

返回给前端的Result类
```java
package com.ilirus.springcloud.entities;

import com.ilirus.springcloud.enums.Status;
import org.jetbrains.annotations.Contract;
import org.jetbrains.annotations.NotNull;

public class CommonResult<T> {
    private Integer code;
    private String message;
    private T data;

    @NotNull
    @Contract("_ -> new")
    public static<T> CommonResult<T> Of(@NotNull Status status) {
        return new CommonResult<T>(status.getCode(), status.getMessage());
    }

    @NotNull
    @Contract("_, _ -> new")
    public static<T> CommonResult<T> OfData(@NotNull Status status, T data) {
        return new CommonResult<T>(status.getCode(), status.getMessage(), data);
    }

    private CommonResult(Integer code, String message) {
        this(code, message, null);
    }

    private CommonResult(Integer code, String message, T data) {
        this.code = code;
        this.message = message;
        this.data = data;
    }

    public Integer getCode() {
        return this.code;
    }

    public String getMessage() {
        return this.message;
    }

    public T getData() {
        return this.data;
    }
}
```
</details>

### 编写枚举类
<details><summary>点击展开</summary>```java
package com.ilirus.springcloud.enums;

import org.jetbrains.annotations.Contract;

public enum Status {
    SUCCESS(200,"sucess"),
    FAIL(400,"fail"),
    NO_DATA_FOUND(404,"not found")
    ;

    private Integer code;
    private String message;

    Status(Integer code, String message) {
        this.code = code;
        this.message = message;
    }

    @Contract(pure = true)
    public Integer getCode() {
        return code;
    }

    @Contract(pure = true)
    public String getMessage() {
        return message;
    }
}
```
</details>

### 编写Dao
<details><summary>点击展开</summary>```java
package com.ilirus.springcloud.dao;

import com.ilirus.springcloud.entities.Payment;
import org.apache.ibatis.annotations.Param;

import java.util.Optional;

public interface PaymentDao {
    public Optional<Payment> getPaymentByID(@Param("id") Long id);

    public int createPayment(Payment payment);
}
```

mapper文件
```xml
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE mapper PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN" "http://mybatis.org/dtd/mybatis-3-mapper.dtd">

<mapper namespace="com.ilirus.springcloud.dao.PaymentDao">

  <resultMap id="BaseResultMap" type="com.ilirus.springcloud.entities.Payment">
    <id column="id" property="id" jdbcType="BIGINT"/>
    <id column="serial" property="serial" jdbcType="VARCHAR"/>
  </resultMap>
  <select id="getPaymentByID" parameterType="Long" resultMap="BaseResultMap">
    select id, serial from payment where id=#{id} limit 1;
  </select>

  <insert id="createPayment" parameterType="Payment" useGeneratedKeys="true" keyProperty="id">
    insert into payment(serial) values(#{serial});
  </insert>

</mapper>
```
</details>

### 编写Service
<details><summary>点击展开</summary>```java
package com.ilirus.springcloud.service;

import com.ilirus.springcloud.entities.Payment;
import org.apache.ibatis.annotations.Param;

public interface PaymentService {
    public Payment getPaymentByID(@Param("id") Long id);

    public int createPayment(Payment payment);
}
```

实现类
```java
package com.ilirus.springcloud.service.impl;

import com.ilirus.springcloud.dao.PaymentDao;
import com.ilirus.springcloud.entities.Payment;
import com.ilirus.springcloud.service.PaymentService;
import org.springframework.stereotype.Service;

import javax.annotation.Resource;

@Service
public class PaymentServiceImpl implements PaymentService {
    @Resource
    private PaymentDao paymentDao;

    @Override
    public Payment getPaymentByID(Long id) throws NullPointerException {
        return paymentDao.getPaymentByID(id).orElse(null);
    }

    @Override
    public int createPayment(Payment payment) {
        return paymentDao.createPayment(payment);
    }
}
```
</details>

### 编写Controller
<details><summary>点击展开</summary>```java
package com.ilirus.springcloud.controller;

import com.ilirus.springcloud.entities.CommonResult;
import com.ilirus.springcloud.entities.Payment;
import com.ilirus.springcloud.enums.Status;
import com.ilirus.springcloud.service.PaymentService;
import lombok.extern.slf4j.Slf4j;
import org.springframework.web.bind.annotation.*;

import javax.annotation.Resource;

@RestController
@RequestMapping("/payment")
@Slf4j
public class PaymentController {
    @Resource
    private PaymentService paymentService;

    @PostMapping("/create")
    public CommonResult create(@RequestBody Payment payment) {
        int res = paymentService.createPayment(payment);
        log.info("插入结果: "+res);

        if(res > 0) {
            return CommonResult.Of(Status.SUCCESS);
        } else {
            return CommonResult.Of(Status.FAIL);
        }
    }

    @GetMapping("/query/{id}")
    public CommonResult queryByID(@PathVariable("id") Long id) {
        Payment payment = paymentService.getPaymentByID(id);
        if(payment != null) {
            return CommonResult.OfData(Status.SUCCESS, payment);
        } else {
            return CommonResult.Of(Status.NO_DATA_FOUND);
        }
    }
}
```
</details>

### 编写测试
在子工程的`src/test/java/<groupId>`目录下创建对应的测试类
<details><summary>点击展开</summary>```java
package com.ilirus.springcloud;

import com.ilirus.springcloud.entities.Payment;
import com.ilirus.springcloud.service.PaymentService;
import org.junit.jupiter.api.AfterAll;
import org.junit.jupiter.api.Assertions;
import org.junit.jupiter.api.BeforeEach;
import org.junit.jupiter.api.Test;
import org.springframework.boot.test.context.SpringBootTest;
import org.springframework.test.annotation.Rollback;
import org.springframework.transaction.annotation.Transactional;

import javax.annotation.Resource;
import java.sql.SQLException;
import java.time.Duration;

@SpringBootTest
class ChildProjectTest {
    @Resource
    PaymentService paymentService;

    @BeforeEach
    void echo() {
        System.out.println("====Test Start====");
    }

    @AfterAll
    static void done() {
        System.out.println("====Test Done.====");
    }

    @Test
    void checkQuery() {
        Payment payment = paymentService.getPaymentByID(1L);
        Assertions.assertNotNull(payment);
    }

    @Test
    @Transactional
    @Rollback
    void checkCreate() throws SQLException {
        Payment payment = new Payment();
        payment.setSerial("test");
        int res = paymentService.createPayment(payment);
        System.out.println(res);
        Assertions.assertNotEquals(res, 0);

        Assertions.assertThrows(SQLException.class, () -> {
            ...
        });
        Assertions.assertTimeout(Duration.ofSeconds(3), () -> {
            ...
        });
    }
}
```
</details>

## 提取子工程中的共同模块
再创建一个子工程，使你的父工程中目录下有两个子工程。

### 再次创建一个子工程
按照之前的方式再创建一个子工程

### 编写pom.xml
```xml
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
  <parent>
    <groupId>com.ilirus.springcloud</groupId>
    <artifactId>irscloud</artifactId>
    <version>1.0-SNAPSHOT</version>
  </parent>
  <modelVersion>4.0.0</modelVersion>

  <artifactId>irs-api-common</artifactId>

  <dependencies>
    <dependency>
      <groupId>org.springframework.boot</groupId>
      <artifactId>spring-boot-devtools</artifactId>
      <scope>runtime</scope>
      <optional>true</optional>
    </dependency>
    <dependency>
      <groupId>org.projectlombok</groupId>
      <artifactId>lombok</artifactId>
      <optional>true</optional>
    </dependency>
    <dependency>
      <groupId>cn.hutool</groupId>
      <artifactId>hutool-all</artifactId>
      <version>5.1.0</version>
    </dependency>
  </dependencies>

</project>
```

### 提取共同模块
将另外两个子工程中共同的模块(entities/enums等)转移到此工程下

### 运行maven clean install命令
运行maven clean install命令，同时检查是否有误

### 修改原先的子工程
在子工程中引入通用包
```xml
<dependency>
  <groupId>com.ilirus.springcloud</groupId>
  <artifactId>irs-api-common</artifactId>
  <version>${project.version}</version>
</dependency>
```

在Run dashboard 中运行项目查看是否有误
