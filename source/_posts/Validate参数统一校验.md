---
title: 使用Validate实现参数统一校验以及自定义校验规则
date: 2020-11-08 22:40:20
tags: [Java]
categories: [Java]
index_img: '/img/202011/title.jpg'
banner_img: '/img/202011/title.jpg'
---

> `JSR-303`是一项标准, `JSR-349`是其的升级版本, 添加了一些新特性, 这项标准规定了一些校验规范, 如`@Null`, `@NotNull`, `@Pattern`, 位于`javax.validation.constraints`包下, 只提供规范不提供实现.  
> `Hibernate Validation`是对这个规范的实践, 他提供了相应的实现, 并增加了一些其他校验注解, 如`@Email`, `@Length`, `@Range`等.

# 引入Validator依赖
```xml
<dependency>
  <groupId>org.hibernate.validator</groupId>
  <artifactId>hibernate-validator</artifactId>
  <version>6.1.6.Final</version>
</dependency>
```

# 配置Validator
`Validator`默认会对所有数据进行校验, 然后返回结果, 如果想要在校验到数据不符合规则时直接返回结果的话, 则需要进行配置
```java
@Configuration
public class ValidatorConfiguration {
  @Bean
  public Validator validator() {
    return Validation.byProvider(HibernateValidator.class)
            .configure()
            // 设置校验到任意数据不符合规则时直接返回结果, 不再对剩余数据进行校验
            .failFast(true)
            .buildValidatorFactory()
            .getValidator();
}
}
```

# 使用Validator对Controller参数进行校验
参数实体类
```java
@Data
public class Param {
  @NotNull(message = "id不能为空")
  private Integer id;

  @NotBlank(message = "name不能为空")
  private String name;

  @NotNull(message = "age不能为空")
  private Integer age;

  @NotNull(message = "sex不能为空")
  private Byte sex;

  private String time;
}
```

<!-- more -->

Controller
```java
@PostMapping("/hello")
@ResponseBody
public String hello(@Validated @RequestBody Param param, BindingResult result) {
  if(result.hasErrors()) {
    return "fail";
  }
  return "success";
}
```

Validator会对参数进行校验，并返回校验结果(`BindingResult`)

## 使用统一异常处理器来处理Validator校验结果
```java
@RestControllerAdvice
@ResponseStatus(HttpStatus.OK)
public class GlobalExceptionHandler {
  // Json格式入参时, 参数不符合规则抛出MethodArgumentNotValidException
  @ExceptionHandler(MethodArgumentNotValidException.class)
  public R<List<Map<String, Object>>> argNotValidErr(MethodArgumentNotValidException e) {
    return R.ofFail(getValidError(e.getBindingResult()));
  }

  // 使用Get请求接口, 参数写在url中时, 参数不符合规则抛出BindException
  @ExceptionHandler(BindException.class)
  public R<List<Map<String, Object>>> binddErr(BindException e) {
    return R.ofFail(getValidError(e.getBindingResult()));
  }

  // 处理Validator的验证结果, 获取所有的不符合规则的参数
  private List<Map<String, Object>> getValidErrors(BindingResult result) {
    if(result == null) {
      return new ArrayList<>(0);
    }

    List<Map<String, Object>> lis = new ArrayList<>(1);
    for(FieldError error : result.getFieldErrors()) {
      lis.add(new HashMap<>(){{
        put("field", error.getField());
        put("message", error.getDefaultMessage());
        put("rejectValue", error.getRejectedValue());
      }});
    }

    return lis;
  }

  // 如果配置了Validator的failFast属性为true, 则可以使用下面的方法处理结果
  private Map<String, Object> getValidError(BindingResult result) {
    if(result == null||result.getFieldError() == null) {
      return new HashMap<>(0);
    }

    FieldError error = result.getFieldError();
    return new HashMap<>(){{
      put("field", error.getField());
      put("message", error.getDefaultMessage());
      put("rejectValue", error.getRejectedValue());
    }};
  }
}
```
