---
title: Validate参数分组校验
date: 2020-11-10 12:30:22
tags: [Java,Validate]
categories: [编程,Validate]
---

# Validator分组校验

定义接口, 接口里面不需要写任何东西, Validator会把接口视为一个一个的**组**
```java
public interface IGet {}

public interface IList {}

public interface IAdd {}

public interface IEdit {}

public interface IDelete {}

// 如果一个组继承了另一个组,
// 则在校验属于这个组的参数时,
// 也会一并校验被继承组的参数
public interface ISexFilter extends Default {}
```

修改参数实体类
```java
@Data
public class Param {
  // groups 定义该字段所属的"组"
  @NotNull(message = "id不能为空", groups = {IGet.class, IEdit.class, IDelete.class})
  @Null(message = "获取列表时不需要ID", groups = IList.class)
  private Integer id;

  @NotBlank(message = "name不能为空", groups = IAdd.class)
  @Null(message = "不能修改name", groups = IEdit.class)
  private String name;

  @NotNull(message = "age不能为空", groups = IEdit.class)
  private Integer age;

  @NotNull(message = "sex不能为空", groups = IEdit.class)
  @NotBlank(message = "sex不能为空", groups = ISexFilter.class)
  private Byte sex;

  // 没有标明groups参数时, 默认属于Default组
  @NotBlank(message = "time不能为空")
  private String time;
}
```

修改Controller
```java
@GetMapping
@ResponseBody
// 如果没有传入要校验的组, Validator默认校验Default组的参数
public String validateTest(@Validated Param param){
  return "success";
}

@GetMapping("/get")
@ResponseBody
public Object get(@Validated(value = {IGet.class, ISexFilter.class}) Param param){
  return service.get(param.getId());
}

@GetMapping("/list")
@ResponseBody
// 这里虽然只指定了校验 IList组 和 ISexFilter组,
// 但由于 ISexFilter组 继承了 Default组,
// 所以实际上是校验属于 IList组, ISexFilter组, Default组 的参数.
// 且由于 time 字段没有指明所属组, 所以 time字段 默认属于 Default组,
// 故而 id(IList组), sex(ISexFilter组), time(Default组) 字段都会被校验.
public List<Object> list(@Validated({IList.class, ISexFilter.class}) Param param){
  return service.list();
}

@PostMapping("/add")
@ResponseBody
public String add(@Validated(value = IAdd.class) @RequestBody Param param){
  return "success";
}

@PatchMapping("/edit")
@ResponseBody
public String edit(@Validated(IEdit.class) @RequestBody Param param){
  return "success";
}

@DeleteMapping("/delete")
@ResponseBody
public String delete(@Validated(IDelete.class) @RequestBody IndexParam param){
  return "success";
}
```

## 定义Validator校验顺序
> 默认情况下, 不同组别的约束验证是无序的, 然而在某些情况下, 约束验证的顺序却很重要. 例如:
> - 第二个组中的校验依赖于一个稳定状态, 而这个稳定状态是由第一个组来进行校验的.
> - 某个组的校验比较耗时, 最优的选择是将其放在最后进行验证.

定义分组接口
```java
public interface IFirstValidate {}

public interface ISecondValidate {}
```

## 定义分组校验顺序
```java
@GroupSequence({
  IFirstValidate.class,
  ISecondValidate.class
})
public interface IValidateGroup {}
```

## 使用

参数实体类
```java
@Data
public class EditParam {
  @NotNull(message = "id不能为空", groups = IFirstValidate.class)
  private Integer id;

  @NotNull(message = "detail不能为空", groups = IFirstValidate.class)
  @Size(min = 1024, message = "detail不能小于1024", groups = ISecondValidate.class)
  private List<Object> detail;
}
```

在控制器中使用
```java
@PutMapping("/edit")
@ResponseBody
public String edit(@Validated(IValidateGroup.class) @RequestBody EditParam param){
  return "success";
}
```