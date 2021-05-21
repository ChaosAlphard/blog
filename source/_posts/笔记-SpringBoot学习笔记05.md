---
title: '[笔记]SpringBoot学习笔记05'
date: 2018-10-19 08:52:40
tags: [Java,JUnit]
categories: [学习笔记,Springboot]
---

# 使用JUnit进行单元测试
## 生成测试类
在需要进行测试的类中 点击右键->Go To->Test 或者 使用快捷键`Ctrl+Shift+T`生成测试类

生成的类
```java
public class CustomDaoTest {
    @Resource private CustomDao dao;

    @Test
    public void findCustomByName() {
    }

    @Test
    public void findCustomByID() {
    }
}
```

<!-- more -->

添加测试
```java
@SpringBootTest
@RunWith(SpringRunner.class)
public class CustomDaoTest {
    @Resource private CustomDao dao;

    @Test
    public void findCustomByName() {
        Custom c = dao.findCustomByName("Ilirus");
        assertEquals(c.getId(),1);
    }

    @Test
    public void findCustomByID() {
        Custom c = dao.findCustomByID(1);
        assertEquals(c.getId(),1);
    }
}
```