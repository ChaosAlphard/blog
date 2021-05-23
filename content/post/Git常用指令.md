---
title: Git常用指令
date: 2019-08-03 20:44:37
tags: [Git]
categories: [编程]
---

# Git常用指令
`git init`: 为当前文件夹初始化Git仓库

`git status`: 查看状态(modified, untracked)

`git add <file|path>`: 添加指定文件/文件夹到暂存区(staging index)

`git commit`: 提交暂存区的文件到版本库  
`git commit -m 'msg'`: 提交暂存区的文件到版本库  
`git commit -am 'msg'`: 跳过`git add`步骤，直接提交所有**工作区(working directory)已追踪**文件

`git log`: 查看提交信息/记录

## Git的三层结构
- `working directory`: 工作区
- `staging index`: 暂存区
- `git directory`: 版本库

## Git文件的四种状态
- `Untracked`: 未被追踪
- `Modified`: 已被修改，但未添加到暂存区
- `Staged`: 添加到了暂存区，但未提交到版本库
- `Committed`: 数据被储存在本地库中