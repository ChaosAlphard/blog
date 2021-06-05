---
title: 'Minecraft Mod 开发手册03: 第一个方块'
description:
date: 2021-06-05T22:42:08+08:00
image: title.png
math:
license:
hidden: false
comments: true
draft: false
tags: [Java,Minecraft]
categories: [编程,Minecraft]
---

# 新建方块
```kotlin
class ModIconBlock: Block(settings) {
  companion object {
    // 使用木板的方块预设
    val settings: Settings = Settings.of(Material.WOOD)
        // 方块硬度
        .strength(2.0F)
        // 方块声音
        .sounds(BlockSoundGroup.WOOD);

    // 方块实例
    val INSTANCE = ModIconBlock();

    // 方块的物品形式实例
    val ITEM_INSTANCE = BlockItem(instance, Item.Settings().rarity(Rarity.RARE).group(ModItemGroup.ITEM));

    val identifier = Identifier("irs_widget_pack", "mod_icon_block")
  }
}
```

# 注册方块
```kotlin
@Suppress("unused")
fun init() {
  // 创建方块标识，方块注册完成后还需要注册物品形式的物品标识
  Registry.register(Registry.BLOCK, ModIconBlock.identifier, ModIconBlock.INSTANCE)
  Registry.register(Registry.ITEM, ModIconBlock.identifier, ModIconBlock.ITEM_INSTANCE)
}
```

# 给方块添加材质
### 添加材质文件

在`resources/assets/<你的Mod注册的命名空间>/textures/block` 文件夹下，创建一个与你添加物品同名的`png` 文件。

### 配置方块状态

在`resources/assets/<你的Mod注册的命名空间>/blockstates` 文件夹下，创建一个与你添加物品同名的`json` 文件。

方块状态文件根据其方块状态确定该方块应使用的模型。由于现在方块还没有所谓状态，所以用空字符串表示所有
```javascript
{
  "variants": {
    "": { "model": "irs_widget_pack:block/mod_icon_block" }
  }
}
```

### 配置方块模型

在`resources/assets/<你的Mod注册的命名空间>/models/block` 文件夹下，创建一个与你添加物品同名的`json` 文件。

方块模型文件定义了方块的形状和材质。使用block/cube_all作为父模型，这将在方块的所有面上设置相同的材质。 

```javascript
{
  "parent": "block/cube_all",
  "textures": {
    "all": "irs_widget_pack:block/mod_icon_block"
  }
}
```

### 配置方块物品模型

在`resources/assets/<你的Mod注册的命名空间>/models/item` 文件夹下，创建一个与你添加物品同名的`json` 文件。

在大多数情况下，方块作为物品时外观还是这个方块。所以，直接继承方块模型即可

```javascript
{
  "parent": "irs_widget_pack:block/mod_icon_block"
}
```

最后，进入游戏即可看到效果
![结果](00.png)
