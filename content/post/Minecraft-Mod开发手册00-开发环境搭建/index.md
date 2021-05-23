---
title: 'Minecraft Mod 开发手册00: 开发环境搭建'
date: 2021-05-20 00:02:00
tags: [Java,Minecraft]
categories: [编程,Minecraft]
image: title.jpg
---

# Fabric开发环境搭建
Minecraft目前有[`Forge`](https://forums.minecraftforge.net/)和[`Fabric`](https://fabricmc.net/)两大Mod平台，这里我选择使用较新的`Fabric`平台。

## 开发工具
- jdk8以上
- 任意IDE

## 创建项目
- 下载`Fabric`官方的[项目初始模板](https://github.com/FabricMC/fabric-example-mod/)，`Kotlin`开发人员可以下载[Kotlin版](https://github.com/natanfudge/fabric-example-mod-kotlin)
- 修改`gradle.properties` 文件
  - 修改`maven_group` 为你的组织名(类似于Maven中的GroupId)
  - 修改`archives_base_name` 为你的模组名(类似于Maven中的ArtifactId)
  - 修改`minecraft_version` 为你的模组的目标版本(你要为哪个Minecraft版本开发Mod)
  - 修改`yarn_mappings`、`loader_version` 与`fabric_version` 为`minecraft_version` 对应的版本，版本对应关系可在[这里](https://fabricmc.net/versions.html)查看
- 修改`src`目录下的目录名称，对应到你的`maven_group`与`archives_base_name`
- 修改`src/resources/fabric.mod.json`
  - `id` 为你的模组的命名空间
  - `entrypoints.main` 为你的模组的入口(main方法所在类)
  - `mixins` 为`mixins.json` 所在位置
- 修改`<modid>.mixins.json`(modid为你的模组的命名空间, fabric.mod.json中id对应的值)
  - `package` 为mixin所在的包名
  - `client` 为mixin的类名
- 最后，将修改好的项目导入到你的IDE中

<!-- more -->

## 修改Gradle构建编译器(可选)
如果你使用的是`IntelliJ IDEA`, 它默认使用Gradle来构建你的项目，而这在Fabric中是不必要的，通过以下步骤来使你的`IntelliJ IDEA`使用自己的编译器
- 在Build Tools -> Gradle 设置项中，修改`Build and run using` 以及`Run tests using` 选项为`IntelliJ IDEA`
- 在Project Structure -> Project 设置项中，修改`Project compiler output` 为`$PROJECT_DIR$/out`

## 启动Minecraft
在Gradle任务中执行runClient 即可启动Minecraft客户端

## Fabric官方Wiki
> https://fabricmc.net/wiki/tutorial:introduction
