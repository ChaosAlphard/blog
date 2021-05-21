---
title: '[笔记]JDBC事务处理'
date: 2018-08-12 18:24:28
tags: [Java]
categories: [Java]
---

# JDBC事务处理
## setAutoCommit
设置是否自动提交事务操作到数据库
```java
conn.setAutoCommit([true|false]);
```
设置为`true`时，会自动提交，无法进行回滚。  
设置为`false`时，需要手动调用`commit()`方法进行提交，或者调用`rollback()`方法进行回滚。

## commit/rollback
对数据库的增删改查等操作，会先保存到缓存里，然后再提交到数据库或者回滚。
- <span style="font-size:24px">commit</span>  
将事务操作提交到数据库。

- <span style="font-size:24px">rollback</span>  
回滚事务操作。

<!-- more -->

举例:
```java
Connection conn=getConnection();
if(conn!=null){
    try {
        conn.setAutoCommit(false);
        dao.insert("1","Test");
        dao.update("1","Edit");
        dao.delete("1");
        conn.commit;
    } catch (SQLException e) {
        conn.rollback;
        e.printStackTrace();
    }
}
```

## close
关闭数据库链接，释放资源。  
注意：最好在close之后将conn设为空值。
```java
conn.close();
conn=null;
```

## isClosed
判断conn链接是否已被关闭。
```java
boolean flag = conn.isClosed();
```

## abort
终止链接  
具体用法...(待补)