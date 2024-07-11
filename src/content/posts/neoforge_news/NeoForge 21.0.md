---
title: NeoForge 21.0 for Minecraft 1.21
published: 2024-07-10
description: "NeoForge 20.5 for Minecraft 1.20.5"
image: "https://cdn.jsdelivr.net/gh/Flandre923/CDN@latest/img/20240710221037.png"
tags: ["NeoForge_new", "Minecraft", "Neoforge"]
category: NeoForge_News
draft: false
---

# 之前的更新
在深入探讨 1.21 带来的变化之前，我们想快速回顾一下 NeoForge 自 1.20.1 以来所做的变化，对于那些尚未更新到 1.20.6 的人来说。您还可以找到每个版本各自的博客文章，其中概述了重要的 Vanilla 更改：

- 1.20.2    
  - 包重命名
  - (EventBus 发生变化)[https://neoforged.net/news/20.2eventbus-changes/]
  - (注册表（Registry）重做)[https://neoforged.net/news/20.2registry-rework/]
- 1.20.3
  -  (Capability 重做)[https://neoforged.net/news/20.3capability-rework/]
-  1.20.4
   -  引入(data map)[https://github.com/neoforged/NeoForge/pull/519]
   -  (网络重构)[https://neoforged.net/news/20.4networking-refactor]
   -  (切换到 Fabric Mixin)[https://github.com/neoforged/FancyModLoader/pull/94]
-  1.20.5
   -  mods.toml 文件已重命名为 neoforge.mods.toml
   -  (第二次网络重做)[https://neoforged.net/news/20.5release/#network-api-rework]
   -  (Tick 事件重构)[https://github.com/neoforged/NeoForge/pull/542]
-  1.20.6
   -   (删除 Event.Result)[https://github.com/neoforged/NeoForge/pull/588]

# 原版 1.21 的变化
现在，让我们来看看 原版 中最重要的变化。

首先，将 ResourceLocation 构造函数设为私有，并替换为静态工厂方法：
- ResourceLocation#fromNamespaceAndPath：接收资源位置的命名空间和路径。这在功能上等同于1.20.6及以下版本中的ResourceLocation(String, String)构造函数。同时附带有#tryBuild方法，如果参数无效，它将返回null而不是抛出异常。
- ResourceLocation#parse：接收由命名空间和路径组成的资源位置字符串，它们之间用冒号(:)分隔。此方法在功能上等同于1.20.6及以下版本中的ResourceLocation(String)构造函数。同时附带有#tryParse方法，如果参数无效，它将返回null而不是抛出异常。
- ResourceLocation#withDefaultNamespace：接收资源位置的路径，并总是使用"minecraft"作为其命名空间。

# Depluralisation  去复数化
一些原本使用复数形式命名的数据文件夹名称已经被重命名。
- tag folders
  - tags/blocks -> tags/block
  - tags/entity_types -> tags/entity_type
  - tags/fluids -> tags/fluid
  - tags/items -> tags/item
  - tags/game_events -> tags/game_event
  - tags/functions -> tags/function
- recipes -> recipe
- advancements -> advancement
- structures -> structure
- loot_tables -> loot_table
- predicates -> predicate
- item_modifiers -> item_modifier
- functions -> function

# 数据驱动的附魔
附魔不再是一个静态的、内置的注册表，而是一个数据包注册表。

随着这一变化，以前接受附魔（Enchantment）的方法现在将接受一个Holder\<Enchantment>（可以从通过等级的RegistryAccess获得的注册表中获取），并且附魔类（Enchantments class）中的所有字段都是ResourceKey\<Enchantment>。

此外，您的代码现在应该使用 EnchantmentEffectComponents 来确定附魔是否应该影响某些内容，而不是依赖于直接的Level检查。

如需了解更多信息，我们建议您查阅 ChampionAsh 的 1.21 (入门手册)[https://gist.github.com/ChampionAsh5357/d895a7b1a34341e19c80870720f9880f]。


# NeoForge变化
NeoForge 1.21 的第一个测试版也带来了一些变化。

## 弃用

已被标记为即将删除的成员大多已被移除。
- IItemExtension#onArmorTick: 请改用Item#inventoryTick代替，其中护甲槽位索引为36、37、38和39。
- ItemHandlerHelper#copyStackWithSize -> ItemStack#copyWithCount
- ItemHandlerHelper#canItemStacksStack -> ItemStack#isSameItemSameComponents
- ComposterBlock#COMPOSTABLES map现在被忽略。请改用data map
- VibrationSystem#VIBRATION_FREQUENCY_FOR_EVENT map 现在被忽略。请改用 data map
- arrot#MOB_SOUND_MAP map现在被忽略。请改用data map

FML 还删除了已弃用的成员
- ModLoadingContext#registerConfig 方法被 ModContainer#registerConfig 替换
- FMLJavaModLoadingContext#get 已被移除。直接替代的是ModLoadingContext#get，然而不鼓励使用它。你应该尽可能使用直接引用你的容器，并考虑模块的事件总线是作为模块类构造函数参数提供的。
- DistExecutor 已被删除且未替换。您应该使用 if (FMLLoader.dist == \<dist>) 检查，或者为客户端和通用代码设置不同的入口点。

## 枚举拓展重做（在21.0.10-beta引入）
枚举扩展已经被重新设计，以解决之前方法的几个不足之处。现在不是通过在目标枚举上调用静态方法来在任意时间添加新条目，而是通过在枚举扩展的JSON文件中指定它们来添加值，这个JSON文件用于在类加载枚举时添加新值。JSON文件是通过在neoforge.mods.toml文件中的[[mods]]块的enumExtensions键来指定的。

文件中指定的条目包括目标枚举、字段名称、要使用的构造函数以及参数（指定为常量数组、对枚举代理类型字段的引用或对方法的引用）：


```json
{
  "entries": [
    {
      "enum": "net/minecraft/world/item/ItemDisplayContext",
      "name": "EXAMPLEMOD_STANDING",
      "constructor": "(ILjava/lang/String;Ljava/lang/String;)V",
      "parameters": [ -1, "examplemod:standing", null ]
    }
  ]
}
```
有关如何使用新系统的更多信息，请参阅(文档)[https://docs.neoforged.net/docs/advanced/extensibleenums/]；有关此更改动机的更多详细信息，请参阅( FML PR)[https://github.com/neoforged/FancyModLoader/pull/148]。

## Damage Pipeline 重做(21.0.31-beta 中引入 )
在 NeoForge 21.0.31-beta 中，damage event已被重新设计。
这一变化背后的动机是，以前命名和记录不当的事件引起了持续的混乱，尤其是在初学者中。

### DamageContainer
DamageContainer 是通过伤害事件公开的一个新类。这个对象允许对Damage Pipeline的所有方面进行修改，包括吸收和无敌时间。

您可以通过addModifier方法修改不同类型的减免（护甲、附魔、生物效果和吸收）。

```java
container.addModifier(DamageContainer.Reduction.ARMOR, (container, currentReduction) -> Math.max(0, currentReduction - 1));
```

### EntityInvulnerabilityCheckEvent 

这个新事件代表了模组开发者可以取消伤害的最早时间点，并且可以用来覆盖那些在原版游戏中本应处于无敌状态的实体。

当实体受到攻击时，即使该攻击实际上不会伤害该实体，该事件也始终会被触发。

### LivingIncomingDamageEvent (formerly LivingAttackEvent)
这是伤害管道的第一个事件。大多数对即将到来的伤害的修改应该在这次事件中进行。

### LivingShieldBlockEvent (formerly ShieldBlockEvent)
此事件可用于确定实体是否确实通过护盾阻挡了伤害，如果是，则阻挡了多少伤害。

### LivingDamageEvent.Pre (formerly LivingDamageEvent)
这个事件是最后一个可以修改最终伤害的点，或者可以将伤害减少到0。然而，这个事件是不可取消的，因为在它触发时，护甲和盾牌的耐久度损失已经发生了。

### LivingDamageEvent.Post (formerly LivingHurtEvent)
这是伤害管道的最后一个事件。它表示所有伤害已经应用到实体上，并且只能用于对应用的伤害做出反应（例如，减少自定义的饥饿或口渴值）。

### ILivingEntityExtension#onDamageTake
此方法已添加到生物实体中，以便它们能够对已经施加的伤害做出反应，而无需覆盖 actuallyHurt 并调用 super。

我们要感谢 Caltinor 在 Pull Request 方面所做的工作，您可以查阅该请求以获取(更多信息。)[https://github.com/neoforged/NeoForge/pull/792]

## PlantType system 已替换(introduced in NeoForge 21.0.39-beta)

最初的 PlantType 系统存在缺陷并且处理起来相当混乱。相反，我们删除了该系统并添加了 SpecialPlantable 项目接口，在 canSustainPlant block方法中进行了修补，并添加了 neoforge:villager_farmlands block tag。

SpecialPlantable 接口允许模组更容易地判断一个模组化的物品是否可以生成植物，并且公开了方法，方便检查某个位置是否适合该物品生长，以及生成植物本身。

内置的canSustainPlant方法允许方块强制支持许多植物，并且现在与所有原版植物兼容。如果模组化植物的方块覆盖了canSurvive方法而没有调用super，那么模组化植物将需要调用这个方法。

SpecialPlantable方块标签现在让农民村民更容易检测并种植在模组化的方块上。这也可以被模组用来判断一个模组化的方块是否类似于耕地。

# ToolActions 重命名为 ItemAbilities (重命名在 NeoForge 21.0.40-beta)
为了帮助推广我们的ToolActions系统，它已被重命名为ItemAbilities，以帮助显示它可以用于不仅仅是工具，也不仅仅是代表动作。这个系统是标签的一个特殊替代方案，用于当模组需要进行对物品堆叠敏感的检查时，其中物品可以处于许多不同的状态并具有不同的能力。它对于跨模组兼容性很有用，是对物品堆叠敏感的，而无需添加编译时依赖。NeoForge提供的原版工具的ItemAbilities仍然存在，例如shears_dig，以验证使用的物品堆叠是否应该允许剪切模组化的内容。

# 实验 Gradle 插件
我们目前正在开发一个新的实验性 (Gradle 插件)[https://github.com/NeoForged/ModDevGradle]，专注于更简单的构建脚本和改进的缓存。您可以在此处找到有关其使用的信息，并在我们的 (Discord 服务器)[https://discord.neoforged.net/]上的(频道)[https://discord.com/channels/313125603924639766/1239579489617580072]中提供支持。

NeoGradle 将继续受到支持。

# 关于稳定性的说明
与 1.20 生命周期的情况一样，我们预计 1.21（或 1.21.1）将成为 1.21 生命周期中重点关注的稳定版本包。

这意味着 1.21 可能会有更长的测试期，以便我们可以确保来年使用的版本包尽可能好。

然而，我们将一如既往，避免做出无用的重大更改，并确保在我们的 Discord 服务器的开发公告频道中宣布它们。如果您想及时了解最重要的更改，请务必选择 Dev Announcement Pings 角色！

一如既往，我们要感谢大家在 1.20 生命周期中做出的贡献，我们希望在未来几个月内看到为 NeoForge 开发更多模组。

