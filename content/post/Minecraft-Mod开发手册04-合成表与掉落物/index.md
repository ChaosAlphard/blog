---
title: "Minecraft Mod 开发手册04: 合成表与掉落物"
description:
date: 2021-06-06T02:44:16+08:00
image: title.png
math:
license:
hidden: false
comments: true
draft: false
tags: [Java,Minecraft]
categories: [编程,Minecraft]
---

# 添加合成表

在`resources/data/<你的Mod注册的命名空间>/recipes` 文件夹下，创建一个`json` 文件，名称没有要求，但最好以被添加合成表的物品的名称为前缀。

## 添加无序合成表

以`mod_icon` 为例

`mod_icon.json`
```javascript
{
  // 指定该合成表为无序合成
  "type": "minecraft:crafting_shapeless",
  // 指定合成需要的原料, 物品名字格式为 <命名空间>:<物品名称>
  "ingredients": [{
    "item": "minecraft:melon_slice"
  }, {
    "item": "minecraft:carrot"
  }, {
    "item": "minecraft:sweet_berries"
  }, {
    "item": "minecraft:pumpkin_pie"
  }, {
    "item": "minecraft:honey_bottle"
  }, {
    "item": "minecraft:milk_bucket"
  }],
  // 指定合成结果
  "result": {
    "item": "irs_widget_pack:mod_icon",
    "count": 4
  }
}
```

给`mod_icon` 添加第二个合成表

`mod_icon_from_mod_icon_block.json`
```javascript
{
  "type": "minecraft:crafting_shapeless",
  "ingredients": [{
    "item": "irs_widget_pack:mod_icon_block"
  }],
  "result": {
    "item": "irs_widget_pack:mod_icon",
    "count": 8
  }
}
```

## 添加有序合成表

以`mod_icon_block` 为例

`mod_icon_block.json`
```javascript
{
  // 指定该合成表为有序合成
  "type": "minecraft:crafting_shaped",
  // 指定合成配方, 其中"I" 可以是'A-Z'或'#'之中的任意一个值
  "pattern": [
    "III",
    "I I",
    "III"
  ],
  // 解释合成表中的字符代表哪种物品
  "key": {
    "I": {
      "item": "irs_widget_pack:mod_icon"
    }
  },
  "result": {
    "item": "irs_widget_pack:mod_icon_block",
    "count": 1
  }
}
```

![recipes](recipes.gif)

# 添加掉落物

在`resources/data/<你的Mod注册的命名空间>/loot_tables/blocks` 文件夹下，创建一个与被添加掉落表的物品的名称相同的`json` 文件。

如果是实体，则应放在`loot_tables/entities`文件夹下

`mod_icon_block.json`
```javascript
{
  // 指定类型为方块
  "type": "minecraft:block",
  // 指定掉落物随机池
  "pools": [
    {
      // 指定随机次数
      "rolls": 1,
      // 指定掉落实体
      "entries": [
        {
          // 掉落物类型
          "type": "minecraft:item",
          // 掉落物名称
          "name": "irs_widget_pack:mod_icon",
          // 决定掉落物掉落的函数
          "functions": [{
            "function": "minecraft:set_count",
            "count": {
              "min": 8,
              "max": 8,
              "type": "minecraft:uniform"
            }
          }]
        }
      ],
      // 额外的掉落条件
      "conditions": [
        {
          "condition": "minecraft:survives_explosion"
        }
      ]
    }
  ]
}
```

![loot](loot.gif)
