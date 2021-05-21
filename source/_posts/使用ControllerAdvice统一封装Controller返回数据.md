---
title: 使用ControllerAdvice统一封装Controller返回数据
date: 2021-01-10 08:02:12
tags: [Java, SpringBoot]
categories: [Java]
index_img: '/img/202012/title.jpg'
banner_img: '/img/202012/title.jpg'
---

在给前端提供Api接口时，通常会约定好一个统一返回格式，这样方便前端处理返回结果

```java
@RestController
@RequestMapping("/index")
public class IndexController {

  @GetMapping
  public R<Data> index() {
    return R.ofSuccess(new Data());
  }
}
```

但每次都要用统一返回类手动封装一次再返回还是比较麻烦的，可以使用`@ControllerAdvice` 注解来进行统一封装，避免每次手动封装

**定义ControllerAdvice**

```java
// 这里的annotations = ApiResult.class 代表只有在类上有@ApiResult 注解的Controller 才会被处理
// 如果不写则默认对所有Controller 进行处理
@RestControllerAdvice(annotations = ApiResult.class)
public class ResponseAdvice implements ResponseBodyAdvice<Object> {
  @Override
  public boolean supports(MethodParameter returnType, Class<? extends HttpMessageConverter<?>> converterType) {
    // 判断返回类型是否已经是定义好的统一返回类型, 以及@ApiResult 注解是否启用
    // 返回true 则表示进入下一步处理, 返回false 则表示不做处理, 原样返回
    return !returnType.getParameterType().isAssignableFrom(R.class)&&
            !returnType.hasMethodAnnotation(ApiResult.Disable.class);
  }

  @Override
  public Object beforeBodyWrite(Object body, MethodParameter returnType, MediaType selectedContentType, Class<? extends HttpMessageConverter<?>> selectedConverterType, ServerHttpRequest request, ServerHttpResponse response) {

    return R.ofSuccess(body);
  }
}
```

<!-- more -->

**定义注解**
```java
@Target(ElementType.TYPE)
@Retention(RetentionPolicy.RUNTIME)
@Documented
public @interface ApiResult {

    @Target(ElementType.METHOD)
    @Retention(RetentionPolicy.RUNTIME)
    @Documented
    public @interface Disable {}
}
```

**使用**
```java
@RestController
@RequestMapping("/index")
@ApiResult
public class IndexController {

  @GetMapping
  public Data index() {
    return new Data();
  }

  @GetMapping("/string")
  // 不做处理直接返回
  @ApiResult.Disable
  public String returnStr() {
    return "success";
  }
}
```

顺嘴提一句, 使用组合注解也是可以的
```java
@Target(ElementType.TYPE)
@Retention(RetentionPolicy.RUNTIME)
@Documented
@ApiResult
@WithExceptionHandler
public @interface ApiResultWithExceptionHandler {
}
```

```java
@RestController
@RequestMapping("/index")
@ApiResultWithExceptionHandler
public class IndexController {

  @GetMapping
  public Data index() {
    return new Data();
  }
}
```

![01](https://erina.gitee.io/blog/img/202012/01.png)

等会，还没完，转换其他类型的时候都是正常的，但是如果遇到String类型，就会报错 `R cannot be cast to class String`

这是因为在`Controller` 返回`String` 类型时，`Spring` 会优先使用`StringHttpMessageConverter`来进行转换处理，而我们又在统一处理器里面把类型更换为了自定义的统一返回类，所以会导致转换出错

解决的办法有三种

第一种: 在处理器中做判断, 为`String` 的时候手动格式化为统一返回类对应的`Json`格式字符串
```java
@Override
public Object beforeBodyWrite(Object body, MethodParameter returnType, MediaType selectedContentType, Class<? extends HttpMessageConverter<?>> selectedConverterType, ServerHttpRequest request, ServerHttpResponse response) {

  // 判断Controller的返回类型是否是String 类型, 如果是, 则转换为Json格式的String再返回
  if(returnType.getParameterType().isAssignableFrom(String.class)) {
    return R.ofSuccess(body).toJsonString();
  }

  return R.ofSuccess(body);
}
```

第二、三种: 更改`Spring` 的`HttpMessageConverter` 配置，使其用对应的`HttpMessageConverter` 来进行转换
```java
@Component
public class WebConfiguration implements WebMvcConfigurer {
  @Override
  public void configureMessageConverters(List<HttpMessageConverter<?>> converters) {
    // 第二种: 将json 处理的转换器放到第一位, 使得json 转换器优先处理返回值
    // converters.add(0, new MappingJackson2HttpMessageConverter());
    // 第三种: 去掉String类型的转换器, 不使用String类型的转换器
    // converters.removeIf(converter -> converter.getClass().isAssignableFrom(StringHttpMessageConverter.class));
  }
}
```

![02](https://erina.gitee.io/blog/img/202012/02.png)

到此为止才算完成了
