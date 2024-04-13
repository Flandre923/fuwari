---
title: 14 自定义物品模型
published: 2024-04-13
tags: [Minecraft, NeoForge, Tutorial]
description: 14 自定义物品模型 相关教程
image: ./covers/262caee13aae7c6a6ed83abba72197888b0f5c4d.jpg
category: Minecraft NeoForge Tutorial 1.20.4
draft: false
---
# 自定义物品模型

添加一个物品类

```java
public class RubyWand extends Item {
    public RubyWand(){
        super(new Properties().stacksTo(1));
    }
}

```

注册物品

```java
    public static final Supplier<Item> RUBY_WAND = ITEMS.register("ruby_wand", RubyWand::new);


```

添加模型和材质

放到对应的路径下就可以了，和之前一样的。

```
│  ├─examplemod
│  │  ├─blockstates
│  │  ├─models
│  │  │  ├─block
│  │  │  └─item
│  │  └─textures
│  │      ├─block
│  │      └─item

```

这里的模型和材质是使用blockbench画的，这里做一下简单的介绍，详细的介绍可以在B战搜索教学视频，或者看我之前的视频。

添加到创造模式物品栏

略

