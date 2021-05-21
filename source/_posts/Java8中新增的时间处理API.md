---
title: Java8中新增的时间处理API
date: 2020-04-18 23:40:28
tags: [Java]
categories: [Java]
---

# Java8新的时间处理API
**原有的`java.util.Date`与新增的`java.time`有何差异？**
- `java.util.Date`设定为可变类型
- `SimpleDateFormat`是非线程安全的
- 新的API 基于ISO 标准日历系统, 遵循Gregorian 规则
- `java.time`包下的所有类都是不可变类型
- `java.time`包下的所有类都是线程安全的

## `java.time`简单介绍

`java.time`由以下5个包组成:
- `java.time`: 包含值对象的基础包
- `java.time.chrono`: 提供对不同的日历系统的访问
- `java.time.format`: 格式化和解析时间和日期
- `java.time.temporal`: 包括底层框架和扩展特性
- `java.time.zone`: 包含时区支持的类

关于`java.time`下, 类的简介:
- `Instant`: 时间戳
- `LocalDate`: 只包含日期，比如: 2020-01-01
- `LocalTime`: 只包含时间，比如: 12:00:00
- `LocalDateTime`: 包含日期和时间，比如: 2020-01-01T12:00:00
- `Duration`: 时间段, 主要用于计算两个时间之间的差值
- `Peroid`: 时间段, 主要用于计算两个日期之间的差值
- `ZoneOffset`: 时区偏移量，比如: +8:00
- `ZonedDateTime`: 带时区的日期时间
- `Clock`: 时钟，可以获取或指定时区, 也可获取当前的时刻
- `DateTimeFormatter`: 时间格式化

关于`java.time`下**大部分**类的方法**前缀**简介:
- `of`: 静态工厂方法(用类名去调用)。
- `parse`: 静态工厂方法，关注于解析(用类名去调用)。
- `now`: 静态工厂方法，用当前时间创建实例(用类名去调用)
- `get`: 获取某些东西的值。
- `is`: 检查某些东西的是否是true。
- `with`: 返回一个部分状态改变了的时间日期对象拷贝(单独一个with方法,参数为TemporalAdjusters类型)
- `plus`: 返回一个时间增加了的、时间日期对象拷贝(如果参数是负数也能够有minus方法的效果)
- `minus`: 返回一个时间减少了的、时间日期对象拷贝
- `to`: 把当前时间日期对象转换成另外一个，可能会损失部分状态.
- `at`: 把这个对象与另一个对象组合起来，例如: date.atTime(time)。
- `format`: 根据某一个DateTimeFormatter格式化为字符串

<!-- more -->

## 新的API要如何使用

### 获取当前的时间日期
```java
LocalDate date = LocalDate.now();
LocalTime time = LocalTime.now();
// 使用.withNano(0) 来去除纳秒数显示
LocalDateTime datetime = LocalDateTime.now().withNano(0);

System.out.println(date);  // 2020-04-18
System.out.println(time);  // 20:41:02.626861700
System.out.println(datetime);  // 2020-04-18T20:41:02
```

### 与字符串相互转换
```java
final LocalDateTime now = LocalDateTime.now().withNano(0);

final DateTimeFormatter patternToString = DateTimeFormatter.ofPattern("G yyyy年MM月dd号 E a hh时mm分ss秒");
final String byFormat = now.format(patternToString);
System.out.println(byFormat);  // 公元 2020年04月18号 周六 下午 02时11分43秒

final DateTimeFormatter patternToDate = DateTimeFormatter.ofPattern("yyyy-MM-dd HH:mm:ss");
final LocalDateTime byParse = LocalDateTime.parse("2020-04-18 14:11:43", patternToDate);
System.out.println(byParse);  // 2020-04-18T14:11:43
```

### 获取单独的年月日时分秒信息
```java
LocalDate date = LocalDate.now();
int year = date.getYear();
int month = date.getMonthValue();
int day = date.getDayOfMonth();
System.out.println(year);   // 2020
System.out.println(month);  // 4
System.out.println(day);    // 18

LocalTime time = LocalTime.now();
int hour = time.getHour();
int minute = time.getMinute();
int second = time.getSecond();
int nano = time.getNano();
System.out.println(hour);    // 8(时)
System.out.println(minute);  // 2(分)
System.out.println(second);  // 5(秒)
System.out.println(nano);    // 453345500(纳秒)

LocalDateTime dateTime = LocalDateTime.now();
System.out.println(dateTime.getYear());  // 2020
System.out.println(dateTime.getHour());  // 8
```

### 自定义日期时间
```java
final LocalDate date = LocalDate.of(2020, 4, 18);
final LocalTime time = LocalTime.of(8, 20, 45);
final LocalDateTime dateTime = LocalDateTime.of(date, time);
// 使用LocalDateTime.of(2020,4,18,8,10,30)方式也可创建自定义LocalDateTime
// 因为没有指定nano值, 所以不会出现出现纳秒显示
System.out.println(dateTime);  // 2020-04-18T08:20:45 
```

### 时间比较
```java
final LocalDate date1 = LocalDate.now();  // 假设今天为2020-4-18
final LocalDate date2 = LocalDate.of(2020, 4, 18);
// LocalTime和LocalDateTime 同理
// equals: 比较是否相等
if(date1.equals(date2)) {
    System.out.println("相等");
} else {
    System.out.println("不相等");
}

// isAfter: 是否晚于某个日期
// isBefore: 是否早于某个日期
if(date1.isAfter(date2)) {
    System.out.println(date1+" 晚于 "+date2);
} else if(date1.isBefore(date2)) {
    System.out.println(date1+" 早于 "+date2);
}
```

### 检查周期性事件
```java
final LocalDate now = LocalDate.now();

final MonthDay targetMonthDay = MonthDay.of(10, 1);
final MonthDay currentMonthDay = MonthDay.from(now);

if(currentMonthDay.equals(targetMonthDay)) {
    System.out.println("国庆到了");
}
```

### 计算某段时间之前或之后的日期
```java
final LocalDateTime dateTime = LocalDateTime.now().withNano(0);
final LocalDateTime dateTime2 = dateTime
        // 计算当前时间的 一个月后
        .plusMonths(1)
        // 又 一周后
        .plusWeeks(1)
        // 又 2个半天(12h)后
        .plus(2, ChronoUnit.HALF_DAYS)
        // 又 1小时前
        .minusHours(1)
        // 又 2个月前 的时间
        .minus(2, ChronoUnit.MONTHS);
System.out.println(dateTime2);
```

### Clock 时钟类的用法
```java
final Clock clock = Clock.systemUTC();
final long millis = clock.millis();
System.out.println(millis);  // 1587320754163

System.out.println(millis == System.currentTimeMillis());  // true
// Instant.now().toEpochMilli() 也可获取时间戳
System.out.println(millis == Instant.now().toEpochMilli());  // true

final Clock zone = Clock.systemDefaultZone();
ZoneId id = zone.getZone();
System.out.println(id);  // Asia/Shanghai

System.out.println(id.getId().equals(TimeZone.getDefault().getID()));  // true

final LocalDateTime now = LocalDateTime.now().withNano(0);
final ZoneId zoneIdOfHonolulu = ZoneId.of("Pacific/Honolulu");
final ZonedDateTime timeInHonolulu = ZonedDateTime.of(now, zoneIdOfHonolulu);
System.out.println(timeInHonolulu);  // 2020-04-20T02:25:24-10:00[Pacific/Honolulu]
```

### 其他用法
```java
final LocalDateTime now = LocalDateTime.now().withNano(0);
// 判断是否是闰年, LocalDate类才有此方法
if(LocalDate.from(now).isLeapYear()) {
    System.out.println("今年是闰年");
}
// 获取星期
System.out.println(now.getDayOfWeek());  // SATURDAY

final LocalTime time = LocalTime.of(12, 0, 0);
final LocalDate start = LocalDate.of(2018, 8, 18);
final LocalDate end = LocalDate.of(2020, 8, 18);
final LocalDateTime startDate = LocalDateTime.of(start, time);
final LocalDateTime endDate = LocalDateTime.of(end, time);

// 计算两个日期直接的差值, 如果第一个参数的时间晚于第二个参数, 则该类的其他方法结果会返回负数
final Duration between = Duration.between(startDate, endDate);
// 计算天数差值
System.out.println(between.toDays());     // 731 (第一个参数晚于第二个参数则会返回负数-731)
// 计算小时数差值
System.out.println(between.toHours());    // 17544   (-17544)
// 计算分钟数差值
System.out.println(between.toMinutes());  // 1052640 (-1052640)

final Period period = Period.between(LocalDate.from(startDate),LocalDate.from(endDate));
// 计算月份差值
System.out.println(period.toTotalMonths()); // 24
```
