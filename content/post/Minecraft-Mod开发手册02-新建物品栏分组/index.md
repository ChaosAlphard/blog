---
title: 'Minecraft Mod 开发手册02: 新建物品栏分组'
description:
date: 2021-05-23T23:01:27+08:00
image: title.png
math:
license:
hidden: false
comments: true
draft: false
tags: [Java,Minecraft]
categories: [编程,Minecraft]
---

# 注册物品栏分组
新建一个类，用于存放自定义的物品栏分组的实例
```kotlin
class ModItemGroup {
  companion object {
    @JvmStatic
    val ITEM: ItemGroup = FabricItemGroupBuilder
      .build(Identifier("irs_widget_pack","item"))
      { ItemStack(ModIcon.instance) }
  }
}
```
`FabricItemGroupBuilder.build`方法接收两个参数，第一个是物品栏分组的名称，第二个是显示的图标

# 将物品放入自定义物品栏中
修改之前定义好的物品
```kotlin
@JvmStatic
val settings: Settings = Settings()
  // 物品分组修改为我们自定义的分组
  .group(ModItemGroup.ITEM)
```

# 添加翻译
修改之前的语言文件，按照对应的格式，为新增的物品分组添加翻译
```javascript
{
  "itemGroup.irs_widget_pack.item": "物品"
}
```

进入游戏，即可看到效果
![成果](title.png)
