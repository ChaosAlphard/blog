---
title: VSCode配置Springboot开发环境
date: 2019-10-03 20:36:06
tags: [Java,SpringBoot]
categories: 
- [VSCode]
- [Java]
- [Springboot]
---

虽说Springboot的首选开发IDE是IntelliJ IDEA，然而对于老爷机来说IDEA太过庞大，对于因为某些原因而无法使用主力开发电脑时，使用VSCode来开发Springboot也不失为一个不错的选择。

![VSCode](https://s2.ax1x.com/2019/10/03/u0jgIA.jpg)

# 安装开发扩展
在商店中搜索`Java Extension Pack` 扩展，并安装。

这是一个扩展包，VSCode 会自动安装关联的扩展：
- Cloudfoundry Manifest YML Support
- Concourse CI Pipeline Editor
- Debugger for Java
- Java Dependency Viewer
- Java Test Runner
- Language Support for Java(TM) by Red Hat
- Maven for Java

<!-- more -->

![JavaExt](https://s2.ax1x.com/2019/10/03/u0I2c9.jpg)

在商店中搜索`Spring Boot Extension Pack` 扩展，并安装。

这是一个扩展包，VSCode 会自动安装关联的扩展：
- Spring Boot Dashboard
- Spring Boot Tools
- Spring Initializr Java Support
- Visual Studio IntelliCode

![MavenExt](https://s2.ax1x.com/2019/10/03/u0IstU.jpg)

# 配置Java与Maven路径
打开VSCode的设置文件(setting.json)， 写入以下配置
```json
{
  // java路径
  "java.home": "C:/Software/java11/jdk",
  // maven配置文件路径
  "java.configuration.maven.userSettings": "E:/RNC/Tool/maven-3.6.1/conf/settings.xml",
  // 修改pom.xml后导入依赖的动作(automatic 为自动下载与导入)
  "java.configuration.updateBuildConfiguration": "automatic",
  // maven主程序路径
  "maven.executable.path": "E:/RNC/Tool/maven-3.6.1/bin/mvn",
  // 不显示在VSCode文件视图中的文件
  "files.exclude": {
    "**/.classpath": true,
    "**/.project": true,
    "**/.settings": true,
    "**/.factorypath": true,
    // 文件夹
    ".mvn": true,
    ".vscode": true,
    "target": true
  },
}
```

![Config](https://s2.ax1x.com/2019/10/03/u0IyhF.jpg)

# 使用方式
按下`F1`，输入`spring initializr`，选择`Generate a Maven Project`选项即可。

![Init](https://s2.ax1x.com/2019/10/03/u0IrkT.jpg)

选中想要的插件按回车即可，结束选择的方式是选中`Selected x dependencies`选项，并按`Enter`键。

![Init](https://s2.ax1x.com/2019/10/03/u0Ig1J.jpg)

# 配置启动项
在`.vscode/launch.json`文件中写入以下内容：
```json
"configurations": [
  {
    "type": "java",
    "name": "DemoApplication",
    "request": "launch",
    "mainClass": "com.example.springdemo.DemoApplication",
    "projectName": "springdemo"
  }
]
```

保存后再按下`F5` 即可启动项目。

![Start](https://s2.ax1x.com/2019/10/03/u0IRXR.jpg)

`Gradle`也是类似的方式

![Start](https://s2.ax1x.com/2019/10/03/u0Ifn1.jpg)