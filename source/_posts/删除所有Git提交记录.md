---
title: 删除所有Git提交记录
date: 2019-07-14 19:11:26
tags: [Git]
categories: [版本控制]
---

# 删除所有提交记录

一种比较取巧的方法
- 新建分支 `git checkout --orphan latest_branch`
- 添加所有文件 `git add -A`
- 提交更改 `git commit -am "merge commit"`
- 删除分支 `git branch -D master`
- 重命名当前分支 `git branch -m master`
- 强制更新存储库 `git push -f origin master`