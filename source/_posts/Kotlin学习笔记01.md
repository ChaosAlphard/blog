---
title: Kotlin学习笔记01
date: 2018-12-05 16:43:44
tags: [Kotlin]
categories: [学习笔记,Kotlin]
---

# Kotlin项目的创建
## 创建一个Kotlin项目
- Create Project -> Kotlin
  - 选择Kotlin/JVM 或者 Kotlin/JS
  - 选择SDK
  - 选择Library
- 创建包
- 创建`.kt`文件

在创建的`.kt`文件中写入以下代码，运行。
```kotlin
fun main(args: Array<String>){
    println("Hello Kotlin!")
}
```

## 使用Spring Boot + MyBatis 创建Web项目
- Create Project -> Spring Initializr
  1. 选择Initializr Service URL (https://start.spring.io)
  2. Language 选择 Kotlin
  3. 选择插件
    - Core -> DevTools
    - Web -> web
    - Template Engines -> Thymeleaf
    - SQL -> MySQL, MyBatis
  4. 完成

<!-- more -->

### 配置文件
使用`application.properties`文件或者`application.yml`文件皆可  
这里我们使用`application.yml`文件，注意空格
```yml
spring:
  datasource:
    url: jdbc:mysql://localhost:3306/tieba?useSSL=false&serverTimezone=GMT
    username: usr
    password: pwd
    driver-class-name: com.mysql.jdbc.Driver
```

### 编写实体类
```kotlin
class User {
    var uid: Int? = null
    var account: String? = null
    var username: String? = null
    var password: String? = null
    var adminlv: Int? = null

    override fun equals(other: Any?): Boolean {
        if (this === other) return true
        if (javaClass != other?.javaClass) return false

        other as User

        if (uid != other.uid) return false
        if (account != other.account) return false
        if (username != other.username) return false
        if (password != other.password) return false
        if (adminlv != other.adminlv) return false

        return true
    }

    override fun hashCode(): Int {
        var result = uid ?: 0
        result = 31 * result + (account?.hashCode() ?: 0)
        result = 31 * result + (username?.hashCode() ?: 0)
        result = 31 * result + (password?.hashCode() ?: 0)
        result = 31 * result + (adminlv ?: 0)
        return result
    }

    override fun toString(): String {
        return "User{" +
            "UID=$uid, " +
            "Account='$account', " +
            "Username='$username', " +
            "Password='$password', " +
            "Adminlv=$adminlv" +
        "}"
    }
}
```

### 查询接口
```kotlin
@Mapper
interface UserMapper {

    @Select("select * from user where UID = #{uid}")
    fun findUserByID(@Param("uid") phone: String): User

    @Select("select * from user")
    fun findAllUser(): List<User>

    @Insert("insert into user values(null,#{aot},#{usr},#{pwd},1)")
    fun addUser(@Param("aot") account: String,
                @Param("usr") username: String,
                @Param("pwd") password: String): Int
}
```

### 业务层
**接口**
```kotlin
interface UserService {

    fun findAllUser(): List<User>

    fun addUser(users: List<User>): Int
}
```

**实现**
```kotlin
@Service("UserService")
class UserServiceImpl : UserService {
    @Resource
    private lateinit var userDao: UserDao

    override fun findAllUser(): List<User> {
        return userDao!!.findAllUser()
    }

    @Transactional
    override fun addUser(user: User): Int {
        return userDao!!.addUser(user.account,user.username,user.password)
    }
}
```

### 控制器
```kotlin
@Controller
@RequestMapping("/")
class HomePage {
    @Resource
    private lateinit var us: UserService

    @GetMapping("")
    fun home(model: Model): String {
        model.addAttribute("res", us?.findAllUser())
        return "index.html"
    }
}
```

**`lateinit`表示延迟初始化**

### 网页模板
```html
<!DOCTYPE html>
<html lang="en" xmlns:th="http://www.thymeleaf.org">
<head>
    <meta charset="UTF-8">
    <title>Title</title>
</head>
<body>
<table>
    <tr>
        <th>UID</th>
        <th>账号</th>
        <th>昵称</th>
        <th>密码MD5</th>
        <th>权限等级</th>
    </tr>
    <tr th:each="it:${res}">
        <td th:text="${it.uid}"></td>
        <td th:text="${it.account}"></td>
        <td th:text="${it.username}"></td>
        <td th:text="${it.password}"></td>
        <td th:text="${it.adminlv}"></td>
    </tr>
</table>
</body>
</html>
```