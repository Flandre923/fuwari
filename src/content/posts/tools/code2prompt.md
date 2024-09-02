---
title: code2prompt
published: 2024-09-12
tags: ['AI tools']
description: 一个工具将整个项目转为一个可上传给AI的prompt
image: https://cdn.jsdelivr.net/gh/Flandre923/CDN/img/5c84a430-6926-11ef-a1c6-b81ea485754c.jpg
category: Tools
draft: false
---


# code2prompt

[mufeedvh/code2prompt: A CLI tool to convert your codebase into a single LLM prompt with source tree, prompt templating, and token counting. (github.com)](https://github.com/mufeedvh/code2prompt/)

‍

# 简介

一个CLI工具，用于将代码库转换为具有源代码树、提示模板和令牌计数的单个LLM提示。

> 说白了就是一个将代码转为一个单个的文档，这个文档包含了整个项目的代码，你进行提问的时候可以将这个文档附上作为AI回答问题的参考。

‍

这里我将一个的自己的之前的附魔的教程的项目转为了md文档。

[output.md](assets/output-20240815134923-ai6crcw.md)

‍

我们使用通义千问作工具，然后我们提问时候将对应的md文档一起上传上去。进行提问：	

​![image](https://cdn.jsdelivr.net/gh/Flandre923/CDN/img/5d8fd321-6926-11ef-bf5c-b81ea485754c.png)​

我首先提问了这个项目的结构和作用，可以看到回到了相关文件的作用是什么。

‍

之后我提问了：

>  是如何添加一个新的附魔的

> ‍
>
> 为了添加一个新的附魔到 Minecraft 模组中，你需要完成几个步骤。根据提供的代码片段，这里是如何添加两个新的附魔：“my\_auto\_smelt”和“my\_brittle\_curse”的过程：
>
> 1. **定义附魔资源键**： 在 `ModEnchantments`​ 类中，首先定义了两个附魔资源键：
>
>     ```java
>     1public static final ResourceKey<Enchantment> MY_AUTO_SMELT = key("my_auto_smelt");
>     2public static final ResourceKey<Enchantment> MY_BRITTLE_CURSE = key("my_brittle_curse");
>     ```
> 2. **引导方法**： 使用 `bootstrap`​ 方法初始化附魔的注册：
>
>     ```java
>     1public static void bootstrap(BootstrapContext<Enchantment> context) {
>     2  // 获取各种注册表的持有者获取器
>     3  HolderGetter<DamageType> holdergetter = context.lookup(Registries.DAMAGE_TYPE);
>     4  HolderGetter<Item> holdergetter2 = context.lookup(Registries.ITEM);
>     5  // 注册自定义附魔"my_auto_smelt"
>     6  register(context, MY_AUTO_SMELT, createAutoSmeltEnchantment(holdergetter2, context));
>     7  // 注册自定义附魔"my_brittle_curse"
>     8  register(context, MY_BRITTLE_CURSE, createBrittleCurseEnchantment(holdergetter2, context));
>     9}
>     ```
> 3. **注册附魔**：`register`​ 方法用于实际注册附魔：
>
>     ```java
>     1private static void register(BootstrapContext<Enchantment> context, ResourceKey<Enchantment> key, Enchantment.Builder builder) {
>     2  context.register(key, builder.build(key.location()));
>     3}
>     ```
> 4. **创建附魔资源键**： 创建附魔资源键的方法：
>
>     ```java
>     1private static ResourceKey<Enchantment> key(String name) {
>     2  return ResourceKey.create(Registries.ENCHANTMENT, ResourceLocation.fromNamespaceAndPath(ExampleMod.MODID, name));
>     3}
>     ```
> 5. **定义附魔**：
>
>     * 对于“my\_auto\_smelt”，定义了一个自动熔炼的附魔，该附魔在挖掘时有一定概率将挖出的物品转化为它们的熔炼版本：
>
>       ```java
>       1register(
>       2  context,
>       3  MY_AUTO_SMELT,
>       4  Enchantment.enchantment(
>       5    Enchantment.definition(
>       6      holdergetter2.getOrThrow(ItemTags.MINING_ENCHANTABLE),
>       7      2, // 最小等级
>       8      1, // 最大等级
>       9      Enchantment.constantCost(25), // 基础成本
>       10      Enchantment.constantCost(50), // 每级额外成本
>       11      8, // 最大可应用等级
>       12      EquipmentSlotGroup.MAINHAND // 适用的装备槽位
>       13    )
>       14  ).withEffect(
>       15    ModEnchantmentEffectComponents.MY_AUTO_SMELT_TOOL.get(),
>       16    new AddValue(LevelBasedValue.constant(1)) // 效果：每级增加1的熔炼值
>       17  )
>       18);
>       ```
>     * 对于“my\_brittle\_curse”，定义了一个脆性诅咒附魔，该附魔会使装备更易损坏：
>
>       ```java
>       1register(
>       2  context,
>       3  MY_BRITTLE_CURSE,
>       4  Enchantment.enchantment(
>       5    Enchantment.definition(
>       6      holdergetter2.getOrThrow(ItemTags.DURABILITY_ENCHANTABLE),
>       7      2, // 最小等级
>       8      1, // 最大等级
>       9      Enchantment.constantCost(25), // 基础成本
>       10      Enchantment.constantCost(50), // 每级额外成本
>       11      8, // 最大可应用等级
>       12      EquipmentSlotGroup.ANY // 适用的装备槽位
>       13    )
>       14  ).withEffect(
>       15    EnchantmentEffectComponents.ITEM_DAMAGE,
>       16    new MultiplyValue(LevelBasedValue.constant(16)), // 效果：每级增加16倍的伤害
>       17    LootItemRandomChanceCondition.randomChance(0.125f) // 12.5%的概率触发
>       18  )
>       19);
>       ```
> 6. **数据生成器**： 在 `ModDatapackBuiltinEntriesProvider`​ 类中，定义了一个 `RegistrySetBuilder`​ 实例来注册附魔相关的条目：
>
>     ```
>     1public static final RegistrySetBuilder BUILDER = new RegistrySetBuilder()
>     2  .add(Registries.ENCHANTMENT, ModEnchantments::bootstrap);
>     ```
> 7. **数据生成**： 这些自定义附魔将会被数据生成器生成到游戏的数据文件中，例如 JSON 文件，以便在游戏中识别这些附魔。
>
> 以上就是添加新的附魔到 Minecraft 模组中的过程。通过上述步骤，你可以为你的模组添加具有独特效果的新附魔。

可以看到他确实在文档中找到一些相关的附魔的代码的例子。

‍

通过这样的方式，可以让你AI从整体项目上帮你理解代码，学习询问代码的含义，理解学习开源项目。

‍

# 安装和使用

https://github.com/mufeedvh/code2prompt/releases

下载你对应的版本就可以了，![image](https://cdn.jsdelivr.net/gh/Flandre923/CDN/img/5e4929b3-6926-11ef-ae73-b81ea485754c.png)，例如我这里是windwos，就直接下载windows的即可。

下载好之后，在所在的文件打开命令行，

‍

```cmd
PS D:\Download> D:\Download\code2prompt-x86_64-pc-windows-msvc.exe D:\code\NEOFORGE1.21 --output=neoforge1_21.md
```

比如我要将我NEOFORGE1.21项目，转为一个单独的md文件，就可以这样输入，enter之后，就会出现一个neoforge1_21.md文件在你的统计目录下。

​![image](https://cdn.jsdelivr.net/gh/Flandre923/CDN/img/5eccba07-6926-11ef-989e-b81ea485754c.png)​

同时复制到你的剪贴板。你按下ctrl+v就可以直接粘贴了。

​![image](https://cdn.jsdelivr.net/gh/Flandre923/CDN/img/5f624447-6926-11ef-a69e-b81ea485754c.png)​

‍

​![image](https://cdn.jsdelivr.net/gh/Flandre923/CDN/img/60036ee2-6926-11ef-88fa-b81ea485754c.png)​

这个md文件中有的项目的目录结构和对应的文件的代码。

‍

向AI提问提问时候你可以附加上这个输出的文件作为AI的参考资料，可以帮助回答你更加有效的答案。

​![image](https://cdn.jsdelivr.net/gh/Flandre923/CDN/img/60d7c5b7-6926-11ef-bac6-b81ea485754c.png)​

> 根据提供的代码片段，我们可以为 `ModEnchantments`​ 类添加适当的 JavaDoc 注释以提高代码的可读性和维护性。以下是带有注释的 `ModEnchantments`​ 类：
>
> ```java
> 1package com.example.examplemod.datagen.item.enchantment;
> 2
> 3import com.example.examplemod.ExampleMod;
> 4import com.example.examplemod.item.enchantment.ModEnchantmentEffectComponents;
> 5import net.minecraft.core.Holder;
> 6import net.minecraft.core.HolderGetter;
> 7import net.minecraft.core.registries.Registries;
> 8import net.minecraft.data.worldgen.BootstrapContext;
> 9import net.minecraft.resources.ResourceKey;
> 10import net.minecraft.resources.ResourceLocation;
> 11import net.minecraft.tags.ItemTags;
> 12import net.minecraft.world.damagesource.DamageType;
> 13import net.minecraft.world.entity.EquipmentSlotGroup;
> 14import net.minecraft.world.item.Item;
> 15import net.minecraft.world.item.enchantment.Enchantment;
> 16import net.minecraft.world.item.enchantment.EnchantmentEffectComponents;
> 17import net.minecraft.world.item.enchantment.LevelBasedValue;
> 18import net.minecraft.world.item.enchantment.effects.AddValue;
> 19import net.minecraft.world.item.enchantment.effects.MultiplyValue;
> 20import net.minecraft.world.level.Level;
> 21import net.minecraft.world.level.block.Block;
> 22import net.minecraft.world.level.storage.loot.predicates.LootItemRandomChanceCondition;
> 23
> 24/**
> 25 * 自定义附魔类，用于定义和注册新的附魔。
> 26 */
> 27public class ModEnchantments {
> 28
> 29    /**
> 30     * 定义一个名为 "my_auto_smelt" 的附魔资源键。
> 31     */
> 32    public static final ResourceKey<Enchantment> MY_AUTO_SMELT = key("my_auto_smelt");
> 33
> 34    /**
> 35     * 定义一个名为 "my_brittle_curse" 的附魔资源键。
> 36     */
> 37    public static final ResourceKey<Enchantment> MY_BRITTLE_CURSE = key("my_brittle_curse");
> 38
> 39    /**
> 40     * 引导方法，用于初始化附魔注册。
> 41     * 
> 42     * @param context Bootstrap 上下文，包含所有必要的注册表持有者获取器。
> 43     */
> 44    public static void bootstrap(BootstrapContext<Enchantment> context) {
> 45        // 获取各种注册表的持有者获取器
> 46        HolderGetter<DamageType> damageTypeGetter = context.lookup(Registries.DAMAGE_TYPE);
> 47        HolderGetter<Enchantment> enchantmentGetter = context.lookup(Registries.ENCHANTMENT);
> 48        HolderGetter<Item> itemGetter = context.lookup(Registries.ITEM);
> 49        HolderGetter<Block> blockGetter = context.lookup(Registries.BLOCK);
> 50
> 51        // 注册自定义附魔 "my_auto_smelt"
> 52        register(context, MY_AUTO_SMELT, createAutoSmeltEnchantment(itemGetter, context));
> 53
> 54        // 注册自定义附魔 "my_brittle_curse"
> 55        register(context, MY_BRITTLE_CURSE, createBrittleCurseEnchantment(itemGetter, context));
> 56    }
> 57
> 58    /**
> 59     * 创建 "my_auto_smelt" 附魔实例。
> 60     * 
> 61     * @param itemGetter 物品注册表的持有者获取器。
> 62     * @param context Bootstrap 上下文。
> 63     * @return 附魔构建器。
> 64     */
> 65    private static Enchantment.Builder createAutoSmeltEnchantment(HolderGetter<Item> itemGetter, BootstrapContext<Enchantment> context) {
> 66        return Enchantment.enchantment(
> 67            Enchantment.definition(
> 68                itemGetter.getOrThrow(ItemTags.MINING_ENCHANTABLE),
> 69                2, // 最小等级
> 70                1, // 最大等级
> 71                Enchantment.constantCost(25), // 基础成本
> 72                Enchantment.constantCost(50), // 每级额外成本
> 73                8, // 最大可应用等级
> 74                EquipmentSlotGroup.MAINHAND // 适用的装备槽位
> 75            )
> 76        ).withEffect(
> 77            ModEnchantmentEffectComponents.MY_AUTO_SMELT_TOOL.get(),
> 78            new AddValue(LevelBasedValue.constant(1)) // 效果：每级增加1的熔炼值
> 79        );
> 80    }
> 81
> 82    /**
> 83     * 创建 "my_brittle_curse" 附魔实例。
> 84     * 
> 85     * @param itemGetter 物品注册表的持有者获取器。
> 86     * @param context Bootstrap 上下文。
> 87     * @return 附魔构建器。
> 88     */
> 89    private static Enchantment.Builder createBrittleCurseEnchantment(HolderGetter<Item> itemGetter, BootstrapContext<Enchantment> context) {
> 90        return Enchantment.enchantment(
> 91            Enchantment.definition(
> 92                itemGetter.getOrThrow(ItemTags.DURABILITY_ENCHANTABLE),
> 93                2, // 最小等级
> 94                1, // 最大等级
> 95                Enchantment.constantCost(25), // 基础成本
> 96                Enchantment.constantCost(50), // 每级额外成本
> 97                8, // 最大可应用等级
> 98                EquipmentSlotGroup.ANY // 适用的装备槽位
> 99            )
> 100        ).withEffect(
> 101            EnchantmentEffectComponents.ITEM_DAMAGE,
> 102            new MultiplyValue(LevelBasedValue.constant(16)), // 效果：每级增加16倍的伤害
> 103            LootItemRandomChanceCondition.randomChance(0.125f) // 12.5%的概率触发
> 104        );
> 105    }
> 106
> 107    /**
> 108     * 注册附魔到指定的上下文中。
> 109     * 
> 110     * @param context Bootstrap 上下文。
> 111     * @param key 附魔资源键。
> 112     * @param builder 附魔构建器。
> 113     */
> 114    private static void register(BootstrapContext<Enchantment> context, ResourceKey<Enchantment> key, Enchantment.Builder builder) {
> 115        context.register(key, builder.build(key.location()));
> 116    }
> 117
> 118    /**
> 119     * 创建附魔资源键。
> 120     * 
> 121     * @param name 资源键的名字。
> 122     * @return 附魔资源键。
> 123     */
> 124    private static ResourceKey<Enchantment> key(String name) {
> 125        return ResourceKey.create(Registries.ENCHANTMENT, new ResourceLocation(ExampleMod.MODID, name));
> 126    }
> 127}
> ```
>
> 这个类现在包含了详细的 JavaDoc 注释，可以帮助其他开发者更好地理解每个方法的作用。

‍

可以看到还是挺有用的， 此外还有一些其他的指令，例如`--include=`​指出那些文件应是包含生成的， ` --exclude=`​ 指出那些文件应该倍排除 `--tokens`​显示提问会使用的token 

```shell
code2prompt path/to/codebase --tokens --encoding=p50k 指定token的编码格式。
```

‍

也可以输出为json

​`code2prompt path/to/codebase --json`​

‍

# 模板

除了基础的转为一个文件之外，你还可以使用和编写template，template是其实就是 你要AI做的事情 + 你的通过项目输出的代码。

>  code2prompt 附带一组用于常见用例的内置模板。您可以在 templates 目录中找到它们。

[code2prompt/templates at main · mufeedvh/code2prompt (github.com)](https://github.com/mufeedvh/code2prompt/tree/main/templates)

### [`document-the-code.hbs`](https://github.com/mufeedvh/code2prompt/blob/main/templates/document-the-code.hbs)​

使用此模板为记录代码生成提示。它将为代码库中的所有公共函数、方法、类和模块添加文档注释。

> I'd like you to add documentation comments to all public functions, methods, classes and modules in this codebase.
>
> For each one, the comment should include:
>
> 1. A brief description of what it does
> 2. Explanations of all parameters including types/constraints
> 3. Description of the return value (if applicable)
> 4. Any notable error or edge cases handled
> 5. Links to any related code entities
>
> Try to keep comments concise but informative. Use the function/parameter names as clues to infer their purpose. Analyze the implementation carefully to determine behavior.
>
> Comments should use the idiomatic style for the language, e.g. /// for Rust, """ for Python, /** */ for TypeScript, etc. Place them directly above the function/class/module definition.
>
> Let me know if you have any questions! And be sure to review your work for accuracy before submitting.

这是其中使用的提示词，安装他的格式你可以编写自己的hbs模板

‍

通过-t 来指出你要使用的模板是

```shell
code2prompt path/to/codebase -t templates/document-the-code.hbs
```

其他的一些模板和例子，大家到GitHub的项目readme中自己观看吧。

这里看一下使用模板的例子，我自己魔改了下，将英文换成了中文：

这是模板的内容

> ​![image](https://cdn.jsdelivr.net/gh/Flandre923/CDN/img/616547da-6926-11ef-8379-b81ea485754c.png)​

‍

​![image](https://cdn.jsdelivr.net/gh/Flandre923/CDN/img/6221e848-6926-11ef-81d1-b81ea485754c.png)​

然后就可以生成对应的文件了，你也可以通过粘贴直接粘贴到输入框中。

不过这可能需要对应的AI支持较大的token进行输入。

‍