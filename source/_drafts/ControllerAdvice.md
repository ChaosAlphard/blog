ControllerAdvice
```java
// annotations 范围限定为在 '类'(type)上带有对应注解的Controller
// assignableTypes 范围限定为对应的类
// basePackages 范围限定为对应的包下的Controller
// value 同basePackages
// basePackageClasses 范围限定为对应的类所在的包
@ControllerAdvice(annotations = {WithExceptionHandler.class})
@ResponseBody
public class Handler {
    @ExceptionHandler(Exception.class)
    @ResponseStatus(HttpStatus.OK)
    public String handler(Exception e) {
        System.out.println(11111);
        return "Handler: "+e.getMessage();
    }
}
```

自定义注解
```java
@Target({ElementType.TYPE})
@Retention(RetentionPolicy.RUNTIME)
@Documented
public @interface WithExceptionHandler {
}
```