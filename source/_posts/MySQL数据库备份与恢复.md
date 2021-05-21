---
title: MySQL数据库备份与恢复
date: 2018-08-11 21:18:01
tags: [MySQL]
categories: [数据库]
---

# 使用mysqldump/source指令备份/恢复数据库
## 备份数据
用法:
```
mysqldump -u [用户名] -p [options] > [输出文件名]
```
示例
```
mysqldump -u root -p > D:\SQLBak\Data.sql
```
### mysqldump常用选项
- <span style="font-size:24px">--databases</span>
指定需要备份的数据库名称  
用法:
```
mysqldump -u [用户名] -p --databases [dbName] > [输出文件名]
```

- <span style="font-size:24px">--all-databases</span>
备份所有的数据库  
用法:
```
mysqldump -u [用户名] -p --all-databases > [输出文件名]
```

<!-- more -->

- <span style="font-size:24px">--tab[=dir_name]</span>
　　不使用此选项，mysqldump仅输出一个sql文件。  
　　使用此选项，mysqldump会对每一个要备份的表输出两个文件，sql文件保存创建表时的sql语句，txt文件储存表中的数据。  
　　dirname即为sql和txt文件输出的路径。

- <span style="font-size:24px">--add-drop-table</span>
　　此选项会在每一个表前面加上drop table if exists 语句。

- <span style="font-size:24px">--add-locks</span>
　　此选项会在insert语句前面加上lock table 语句。

- <span style="font-size:24px">--skip-comments</span>
　　此选项会去掉输出文件中的注释

## 恢复数据
用法
```
mysql -u [用户名] -p < [备份文件]
```
示例
```
mysql -u root -p < D:\SQLBak\Data.sql
```

已登录mysql的情况下
```
mysql> source [备份文件]
```
示例
```
mysql> source D:\SQLBak\Data.sql
```

### 还原--tab选项备份的文本格式的备份文件
```
mysql -u [用户名] -p < [sql备份文件]                  #还原表结构
mysqlimport -u [用户名] -p [数据库名] [txt备份文件]    #还原表数据
```
已登录mysql的情况下
```
source [sql备份文件];
use [数据库名];
mysql> load data infile `[txt备份文件]` into [表名];
```

## 导出查询结果到本地文件
用法
```
mysql -u [用户名] -p --execute="[sql语句]" [options] [数据库名] > [输出文件]
```
示例
```
mysql -u root -p --execute="select * from testtab;" testdb > D:\SQLBak\result.txt
```

### 常用选项
- <span style="font-size:24px">--vertical</span>
使用--vertical选项可将每条记录分为多行显示。

- <span style="font-size:24px">--html</span>
使用--html选项可将结果导出为html格式。

- <span style="font-size:24px">--xml</span>
使用--xml选项可将结果导出为xml格式。