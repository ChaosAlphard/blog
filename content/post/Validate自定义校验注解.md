---
title: Validate自定义校验注解
date: 2020-11-18 20:12:21
tags: [Java,Validate]
categories: [编程,Validate]
---

# 自定义Validator校验注解
通常来说, `Hibernate Validation`提供的注解已经够用了, 但有时候还是需要根据业务自定义校验规则.

## 自定义注解
```java
@Documented
@Retention(RetentionPolicy.RUNTIME)
@Target({
  ElementType.METHOD,
  ElementType.FIELD,
  ElementType.ANNOTATION_TYPE,
  ElementType.CONSTRUCTOR,
  ElementType.PARAMETER,
  ElementType.TYPE_USE
})
@Repeatable(Time.List.class)
// 设置自定义注解实现类
@Constraint(validatedBy = {TimeValidate.class})
public @interface Time {
  boolean allowNull() default false;

  String regexp() default "^(([0-1][0-9])|(2[0-3])):[0-5][0-9]:[0-5][0-9]$";

  String message() default "时间格式错误";

  Class<?>[] groups() default {};

  Class<? extends Payload>[] payload() default {};

  @Documented
  @Retention(RetentionPolicy.RUNTIME)
  @Target({
    ElementType.METHOD,
    ElementType.FIELD,
    ElementType.ANNOTATION_TYPE,
    ElementType.CONSTRUCTOR,
    ElementType.PARAMETER,
    ElementType.TYPE_USE
  })
  public @interface List {
    Time[] value();
  }
}
```

<!-- more -->

## 自定义注解实现
```java
// 需要实现ConstraintValidator, 第一项参数是要校验的注解, 第二项是要校验的值的类型
public class TimeValidate implements ConstraintValidator<Time, String> {
  private boolean allowNull;
  private Pattern regexp;

  // 初始化
  @Override
  public void initialize(Time annotation) {
    allowNull = annotation.allowNull();
    regexp = Pattern.compile(annotation.regexp());
  }

  // 自定义校验实现
  @Override
  public boolean isValid(String value, ConstraintValidatorContext constraintValidatorContext) {
    if(value == null) {
      return allowNull;
    }
    return regexp.matcher(value).matches();
  }
}
```

## 在参数实体类中使用
```java
@Data
public class TimeParam {
  @NotNull(message = "id不能为空")
  private Integer id;

  @Time(allowNull = true)
  private String time;
}
```
