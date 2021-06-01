---
title: Git提交的回滚
date: 2019-09-18 17:46:03
tags: [Git]
categories: [编程]
---

# revert
> `git revert <commit>`

生成一个撤消了`commit` 引入的修改的新提交，然后应用到当前分支。

`commit` 可以通过使用`git log` 来查看

`revert` 是提交一个 撤销了更改的 **新提交**，而不是从项目历史中移除这个提交。这会避免Git丢失项目历史，是一种比较安全的方式。

# reset
> `git reset`

重设暂存区，但不改变工作区，匹配最近的一次提交。

> `git reset --hard`

重设**暂存区和工作区**，匹配最近的一次提交。  
`--hard` 会使git更改工作区中的文件。确定要扔掉本地的开发进度再使用。

> `git reset <commit>`

将暂存区重设到这个提交，但不改变工作区。  
所有`commit` 之后的更改会保留在工作区中，适用于重新提交更干净的项目历史。

> `git reset --hard <commit>`

将暂存区和工作区都重设到这个提交。

> `git reset <file>`

从暂存区移除某个文件，但不改变工作区。

<!-- more -->

# reset 和 revert
>`git revert` 可以针对历史中任何一个提交，而 `git reset` 只能从当前提交向前回溯。比如，你想用 git reset 重设一个旧的提交，你不得不移除那个提交后的所有提交，再移除那个提交，然后重新提交后面的所有提交。

`revert`被设计为撤销**公开**的提交的安全方式，`reset`被设计为重设**本地**更改。  
因为两个命令的目的不同，它们的实现也不一样：`reset`完全地移除了一堆更改，而`revert`保留了原来的更改，用一个新的提交来实现撤销。

# 查看之前的内容
## 检出提交
> `git checkout <commit>`

使用`checkout commit` 可以便捷地将`commit` 加载到电脑上。  
而且**检出提交**是一个**只读**操作，在查看其他`commit`的时候不会对你当前的状态(比如master分支)产生影响。  
在检出提交的时候，`HEAD`直接指向检出的提交。而不是指向分支。(分离HEAD)

## 检出文件
> `git checkout <commit> <file>`

会将工作区中的`file`文件变成`commit`中的那个文件，并将它加入暂存区。

**这会对你当前的工作区中的文件产生影响。**

# 参考资料
> https://www.atlassian.com/git/tutorials/undoing-changes