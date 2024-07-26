---
title: Neoforge 1.21 附魔教程
published: 2024-07-26
tags: [Minecraft1_21, Tutorial]
description: Neoforge Minecraft1.21 附魔教程
image: https://cdn.jsdelivr.net/gh/Flandre923/CDN@latest/img/20240726211455.png
category: Minecraft1_21_NeoForge_Tutorial
draft: false
---

# 参考和鸣谢

(Infamous Jam的json教程)[https://www.youtube.com/watch?v=JbtYrwfcsxY]

附魔在1.21迎来了一个较大的改变，废弃了之前的方式通过数据包的方式可以添加新的附魔，这次我们讲解1.21的附魔的添加以及如何实现逻辑。
# 如何通过数据包的方式添加一个附魔

我们这里给稿子添加一个自动熔炼的附魔,不过这个附魔附魔病没有实际的效果,也就是并不会真的自动熔炼,我们说的是如何添加这个附魔

需要在你的resources/data/{modid}/enchantment 下添加json,下面是一个json

auto_smelt.json
```json
{
    "anvil_cost":8,
    "description":"Auto Smelt",
    "effects":{
    },
    "slots":[
        "mainhand"
    ],
    "max_level":1,
    "exclusive_set":[],
    "max_cost":{
        "base":50,
        "per_level_above_first":0
    },
    "min_cost":{
        "base":25,
        "per_level_above_first":0
    },
    "supported_items":"#minecraft:enchantable/mining",
    "weight":2
}

```
之后你在打开游戏中,搜索auto_smelt,会看到这个附魔的附魔书.

对其中的一些字段讲解:


```json
{
    "anvil_cost":8, // 将附魔附魔的到一个物品上的花费的代价
    "description":"Auto Smelt",//  这里是附魔的描述
    "effects":{ // 
    },
    "slots":[ // 在什么slot上生效
        "mainhand"
    ],
    "max_level":1, // 最大等级
    "exclusive_set":[], // 
    "max_cost":{ // 附魔的相关的最小花费和最大花费，具体效果不清楚
        "base":50,
        "per_level_above_first":0
    },
    "min_cost":{
        "base":25,
        "per_level_above_first":0
    },
    "supported_items":"#minecraft:enchantable/mining", // # 开头表示寻找一个tag，这里找的是原版的mining的tag的物品。表示只能什么物品上附魔
    "weight":2 //出现的权重
}
```

有些字段我也不清楚,不过大家可以直接复制原版的数值,来确定自己的附魔是什么情况,稀有?,出现的频率?,等属性例如最大的cost和最小的cost.weigth等属性.


# 如何通过数据生成的方式添加一个附魔

```java


// 自定义附魔类，用于定义和注册新的附魔
public class ModEnchantments {
    // 定义一个名为"my_auto_smelt"的附魔资源键
    public static final ResourceKey<Enchantment> MY_AUTO_SMELT = key("my_auto_smelt");

    // 引导方法，用于初始化附魔注册
    public static void bootstrap(BootstrapContext<Enchantment> context)
    {
        // 获取各种注册表的持有者获取器
        HolderGetter<DamageType> holdergetter = context.lookup(Registries.DAMAGE_TYPE);
        HolderGetter<Enchantment> holdergetter1 = context.lookup(Registries.ENCHANTMENT);
        HolderGetter<Item> holdergetter2 = context.lookup(Registries.ITEM);
        HolderGetter<Block> holdergetter3 = context.lookup(Registries.BLOCK);

        // 注册自定义附魔"my_auto_smelt"
        register(
                context,
                MY_AUTO_SMELT,
                Enchantment.enchantment(
                                Enchantment.definition(
                                        holdergetter2.getOrThrow(ItemTags.MINING_ENCHANTABLE),
                                        2,
                                        1,
                                        Enchantment.constantCost(25),
                                        Enchantment.constantCost(50),
                                        8,
                                        EquipmentSlotGroup.MAINHAND
                                )
                        )
        );
    }

    // 注册附魔的方法
    private static void register(BootstrapContext<Enchantment> context, ResourceKey<Enchantment> key, Enchantment.Builder builder) {
        context.register(key, builder.build(key.location()));
    }

    // 创建附魔资源键的方法
    private static ResourceKey<Enchantment> key(String name)
    {
        return ResourceKey.create(Registries.ENCHANTMENT, ResourceLocation.fromNamespaceAndPath(ExampleMod.MODID,name));
    }
}
```


```java

// 扩展自DatapackBuiltinEntriesProvider的类，用于提供自定义的数据包内置条目
public class ModDatapackBuiltinEntriesProvider extends DatapackBuiltinEntriesProvider {
    // 定义一个RegistrySetBuilder实例，用于注册附魔相关的条目
    public static final RegistrySetBuilder BUILDER = new RegistrySetBuilder()
            .add(Registries.ENCHANTMENT, ModEnchantments::bootstrap);

    // 构造函数，初始化ModDatapackBuiltinEntriesProvider实例
    public ModDatapackBuiltinEntriesProvider(PackOutput output, CompletableFuture<HolderLookup.Provider> registries) {
        super(output, registries, BUILDER, Set.of(ExampleMod.MODID));
    }
}

```


```java
// 用于注册数据生成器的类，该类通过EventBusSubscriber注解自动注册到MOD总线上
@EventBusSubscriber(modid = ExampleMod.MODID,bus = EventBusSubscriber.Bus.MOD)
public class ModDataGenerator {
    // 订阅GatherDataEvent事件，当数据收集事件触发时执行该方法
    @SubscribeEvent
    public static void gatherData(GatherDataEvent event) {
        DataGenerator generator = event.getGenerator();
        PackOutput output = generator.getPackOutput();
        CompletableFuture<HolderLookup.Provider> lookupProvider = event.getLookupProvider();
        ExistingFileHelper existingFileHelper = event.getExistingFileHelper();

        // 为数据生成器添加一个自定义的数据包内置条目提供者
        generator.addProvider(event.includeServer(),new ModDatapackBuiltinEntriesProvider(output,lookupProvider));
    }
}
```

运行之后可以得到一个generated

```json
{
  "anvil_cost": 8,
  "description": {
    "translate": "enchantment.examplemod.my_auto_smelt"
  },
  "max_cost": {
    "base": 50,
    "per_level_above_first": 0
  },
  "max_level": 1,
  "min_cost": {
    "base": 25,
    "per_level_above_first": 0
  },
  "slots": [
    "mainhand"
  ],
  "supported_items": "#minecraft:enchantable/mining",
  "weight": 2
}
```

# 如何查看的tag

## 第一种方法

所有原版的item附魔相关tag是在data/minectaft/tags/item/enchantable/下面的json文件.

适用于数据包的作者

## 第二方法

第二种方块我们可以直接查看ItemTags的类中的内容即可.

# 如何查看原版的附魔的json内容

# 第一种方法适用于数据包和模组作者

位于原版的包的/data/enchantment/文件下面的json都是附魔的内容,你可以在这里找到原版的附魔的内容,例如保护,时运等.

# 第二种是模组作者

可以在Enchantments类中找到原版的附魔.


# 讲解数据生成中涉及到的类的租用

## Enchantment.Builder
是一个用于构造Enchantment对象的构建器类，目的是为了构造复杂的Enchantment对象的分步骤的方法。设置各种附魔的属性，例如附魔的最大等级，附魔的花费等信息。

是Enchantment的一个静态内部类。

### Builder中的字段Fields

- Enchantment.EnchantmentDefinition 这个类包含了附魔的一些基础的信息，例如生效的slot（位置，例如主手，装备栏等），出现在附魔台的权重，最大等级等元素。
- HolderSet<Enchantment> exclusiveSet 表示排斥的附魔
- Map<DataComponentType<?>, List<?>> effectLists  表示附魔的一些效果，我们下面细说。
- DataComponentMap.Builder effectMapBuilder 用于构建Map的一个构建器

### Builder中的方法Methods

exclusiveWith: 设置不能与此一起应用的附魔。
withEffect: 向附魔添加效果
withSpecialEffect: 设置与附魔相关的特定的附魔效果.
getEffectsList: 获得附魔的效果的List
build: 根据所有指定的属性制作Enchantment对象.

## Enchantment.EnchantmentDefinition

包含了附魔的一些基础信息.

CODEC 编码解码器
supportedItems 支持附魔到什么物品,是一个tag
primaryItems (暂不清楚,代码中直接给了空)
weight 权重(应该是影响附魔台中出现的概率)
maxLevel 附魔的最大等级,例如时运最大3
minCost 最小花费(暂不清楚具体是什么效果,应该是影响附魔台)
maxCost 最大花费
anvilCost 铁砧上花费的费用s
slots 在那些slot上可以生效,例如主手,副手,装备栏等.


# Effect
下面的我们来说Effect的内容.  我们这次使用原版就提供的effect的内容实现一个易碎的附魔,这个附魔可以附魔在任何的有duraction的tag上的物品.这个tag表示有耐久的东西.

我们是原版提供的effect是效果是有一定的概率使得耐久下降16倍.我们这次也提供了.两种方法,一种是直接添加json,另一种是 datagenerator.



## 第一种

位置和之前一致,这里直接给出json的内容.
```json
{
    "_comment":"This is from DQwerty's example code",
    "anvil_cost":8,
    "description":"Brittle",
    "effects":{
        "minecraft:item_damage":[ //  这个effect是指物品耐久损失相关.
            {
                "effect":{ // 我们效果是当耐久下降时候将这个数值乘上16
                    "type":"minecraft:multiply",
                    "factor":16
                },
                "requirements":{ // 我们这个effect触发的情况,这里是随机触发,概率是0.125 这些内容都是原版提供的. 我们等会会介绍从哪里找这些内容
                    "condition":"minecraft:random_chance",
                    "chance": 0.125
                }
            }
        ]
    },
    "slots":[
        "any"
    ],
    "max_level":1,
    "exclusive_set":["minecraft:unbreaking"],
    "max_cost":{
        "base":50,
        "per_level_above_first":0
    },
    "min_cost":{
        "base":25,
        "per_level_above_first":0
    },
    "supported_items":"#minecraft:enchantable/durability",
    "weight":2
}


```

## 第二种通过数据生成的方式

```java
        register(
                context,
                MY_BRITTLE_CURSE,
                Enchantment.enchantment(
                        Enchantment.definition(
                                holdergetter2.getOrThrow(ItemTags.DURABILITY_ENCHANTABLE),
                                2,
                                1,
                                Enchantment.constantCost(25),
                                Enchantment.constantCost(50),
                                8,
                                EquipmentSlotGroup.ANY
                        )
                ).withEffect( // 我们添加的效果是物品耐久损坏 效果是将损坏数值乘上16 触发条件是随机触发.
                        EnchantmentEffectComponents.ITEM_DAMAGE,
                        new MultiplyValue(LevelBasedValue.constant(16)),
                        LootItemRandomChanceCondition.randomChance(0.125f)
                ) // 你可以添加更多的效果
        );
```

## 我们来说怎么去找这些effect和condiction,即效果和触发条件等内容
- 第一种方法是你可以直接去找原版的附魔实现包含的内容,这部分内容我在上述的讲述提到了在哪里可以找到.
- 第二种看withEffect方法需要你提供的effect是一个泛型,不过这个泛型是和你的第一个参数componentType有关的,即componentType觉得了你的E的类型是什么.
  - 而在EnchantmentEffectComponents类中提供了原版的这些Components.
  - 然后你就可以找到对应的泛型的E,然后你点进入就可以看到这个Componetns的对应的Effect的接口.查看实现类,你就可以找到对应的一些可以使用的Effect
  - 而这里的第三个参数是一个LootItemCondition.Builder你可以去看那些实现了这个接口.即可.

其实依靠原版提供的这些Effect已经可以实现很多的功能了,所以原版的datapack的方式就可以实现很多的效果,不需要通过做模组的方式.


# 实现附魔的逻辑

好了到这里为止，我们的对于数据包的内容已经说的差不多了， 如果还有遗漏的请在评论区补充，该说一点关于我们怎么实现自动冶炼的附魔的功能了。

虽然说不说数据包了，不过还是提一嘴关于自动冶炼也是可以实现的，只需要你去修改每一个矿物矿石方块破坏的掉落的奖励列表，添加一项具有自动冶炼附魔的话就掉落对于的烧过的定。就不多说了，可以去看我给出的参考的中的json是怎么实现的，这里我们还是回到模组制作中，应该怎么判断一个itemstack有没有附魔


首先我们使用方块破坏的事件，然后通过事件获得对于的破坏方块的玩家，方块的位置，方块状态，和破坏方块使用的ItemStack，
获得对于方块状态的掉落物，然后遍历我们看掉落物中有没有粗铁，如果是，那么就转化为铁块掉落，如果没有粗铁则正常掉落。
（如果正常要实现这个自动冶炼的逻辑的话，我们可以通过获得对于的掉落物，然后查对于掉落物的合成表有没有对于的熔炉的合成表，如果有就获得这个合成表的对于的烧过的物品。然后掉落这个烧过的物品）

好了来看我们的逻辑吧

```java

@EventBusSubscriber
public class PlayerServerEvent {

    // 订阅方块破坏事件，当玩家破坏方块时触发
    @SubscribeEvent
    public static void onPlayerBreakBlock(BlockEvent.BreakEvent event){

        Player player = event.getPlayer();
        if(event.getLevel() instanceof ServerLevel serverLevel){
            ItemStack mainHandItem = player.getMainHandItem();
            if (ModEnchantmentHelper.hasAutoSmeltEnchantment(serverLevel,mainHandItem)){
                BlockPos pos = event.getPos();
                BlockState blockState = serverLevel.getBlockState(pos);
                List<ItemStack> drops = blockState.getBlock().getDrops(blockState, serverLevel, pos, null);
                for (ItemStack drop : drops) {
                    if (drop.is(Items.RAW_IRON)){
                        blockState.getBlock().popResource(serverLevel,pos,new ItemStack(Items.IRON_BLOCK,1));
                    }else{
                        blockState.getBlock().popResource(serverLevel,pos,drop);
                    }
                }

            }
        }
    }
}
```
附魔的辅助类，在这个类中我们会展示怎么获得物品的所拥有的附魔以及对于的附魔的等级.

```java

/**
 * 辅助类，用于处理与附魔相关的逻辑。
 */
public class ModEnchantmentHelper {

    /**
     * 检查给定的物品堆栈是否具有自动熔炼附魔。
     *
     * @param level 服务器级别，可能用于确定附魔效果是否适用。
     * @param stack 要检查的物品堆栈。
     * @return 如果物品具有自动熔炼附魔，则返回true，否则返回false。
     */
    public static boolean hasAutoSmeltEnchantment(ServerLevel level, ItemStack stack) {
        // 从物品堆栈中获取附魔信息，如果没有则使用空的附魔集合。
        ItemEnchantments itemenchantments = stack.getOrDefault(DataComponents.ENCHANTMENTS, ItemEnchantments.EMPTY);

        for (Object2IntMap.Entry<Holder<Enchantment>> entry : itemenchantments.entrySet()) {// 获得物品的所有附魔
            @Nullable ResourceKey<Enchantment> enchantmentKey = entry.getKey().getKey();
            if (enchantmentKey != null && enchantmentKey.isFor(ModEnchantments.MY_AUTO_SMELT.registryKey())) { // key是附魔，value是附魔的等级
                // 如果附魔键匹配MY_AUTO_SMELT_TOOL的注册键，则记录日志信息。 
                ExampleMod.LOGGER.info("this itemstack " + stack + " has auto smelt enchantment: " + entry.getKey().value());
                return true;
            }
        }

        // 返回最终的检查结果。
        return false;
    }
}

```

# 自定义component和effect

下面我们说下如何增加自己的component和effect,对于一些模组可能会需要这个内容,因为如果你做一个魔法模组,例如有一个魔法增幅的附魔,那么你可能需要一套类似原版增加伤害的效果,例如力量,锋利等.这样的附魔. 那么你就需要做一下对应的component,并在合适的位置对其数值进行修改,让其他人的可以添加附魔修改你的魔法系统的数值,通过数据包的形式.

对于DataComponentType是通过注册的方式来添加,和之前讲解的Item以及Block等类似.下面直接给出代码

## 添加DataComponentType

```java
// 这里我们同样给出的DataComponentType对于的effect是一个List<ConditionalEffect<EnchantmentValueEffect>>的类型,
// 代表你添加的是数值修改EnchantmentValueEffect,具有修改的条件ConditionalEffect


/**
 * 定义了一组用于自动熔炼附魔的组件注册接口。
 * 这个接口使用了Minecraft Forge的注册机制，允许开发者在游戏的注册事件中注册新的组件。
 */
public interface ModEnchantmentEffectComponents {

    /**
     * 创建一个延迟注册器，用于注册与附魔效果组件相关的数据组件类型。
     * 使用ExampleMod的MODID作为注册表的命名空间。
     */
    DeferredRegister<DataComponentType<?>> ENCHANTMENT_COMPONENTS = DeferredRegister.create(
            Registries.ENCHANTMENT_EFFECT_COMPONENT_TYPE, ExampleMod.MODID);

    /**
     * 注册一个名为 "my_auto_smelt_tool" 的新附魔效果组件。
     * 这个组件用于存储与自动熔炼附魔相关的效果列表。
     * 使用了ConditionalEffect的编解码器和LootContextParamSets.ENCHANTED_ITEM参数集来构建列表。
     */
    DeferredHolder<DataComponentType<?>, DataComponentType<List<ConditionalEffect<EnchantmentValueEffect>>>> MY_AUTO_SMELT_TOOL = register(
            "my_auto_smelt_tool",
            listBuilder -> listBuilder.persistent(
                    ConditionalEffect.codec(EnchantmentValueEffect.CODEC, LootContextParamSets.ENCHANTED_ITEM).listOf())
    );

    /**
     * 私有静态方法，用于注册一个数据组件类型。
     * 这个方法接受组件名称和构建器操作符，用于构建并注册组件。
     *
     * @param <T> 组件类型。
     * @param name 组件的名称。
     * @param operator 用于构建数据组件类型的操作符。
     * @return 注册的DeferredHolder对象。
     */
    private static <T> DeferredHolder<DataComponentType<?>, DataComponentType<T>> register(
            String name,
            UnaryOperator<DataComponentType.Builder<T>> operator) {
        return ENCHANTMENT_COMPONENTS.register(
                name,
                () -> operator.apply(DataComponentType.builder()).build()
        );
    }

    /**
     * 静态方法，用于在提供的事件总线上注册所有的组件。
     * 这通常在模组初始化阶段调用，以确保所有组件在游戏加载时被正确注册。
     *
     * @param eventBus 事件总线，用于注册组件。
     */
    public static void register(IEventBus eventBus) {
        ENCHANTMENT_COMPONENTS.register(eventBus);
    }
}

```

## 使用DataComponentType并添加对于的Effect

这部分内容是数据生成,如果你打算使用json书写,请参考上文的内容

```java
        // 注册自定义附魔"my_auto_smelt"
        register(
                context,
                MY_AUTO_SMELT,
                Enchantment.enchantment(
                                Enchantment.definition(
                                        holdergetter2.getOrThrow(ItemTags.MINING_ENCHANTABLE),
                                        2,
                                        1,
                                        Enchantment.constantCost(25),
                                        Enchantment.constantCost(50),
                                        8,
                                        EquipmentSlotGroup.MAINHAND
                                )
                        ).withEffect(
                        ModEnchantmentEffectComponents.MY_AUTO_SMELT_TOOL.get(),// 这里是我们写的东西
                        new AddValue(LevelBasedValue.constant(1))
                )
        );

```

## 怎么获得物品对于的DataComponentType

我们还是用了之前的那个自动冶炼作为例子.

```java
/**
 * 辅助类，用于处理与附魔相关的逻辑。
 */
public class ModEnchantmentHelper {

    /**
     * 检查给定的物品堆栈是否具有自动熔炼附魔。
     *
     * @param level 服务器级别，可能用于确定附魔效果是否适用。
     * @param stack 要检查的物品堆栈。
     * @return 如果物品具有自动熔炼附魔，则返回true，否则返回false。
     */
    public static boolean hasAutoSmeltEnchantment(ServerLevel level, ItemStack stack) {
        // 从物品堆栈中获取附魔信息，如果没有则使用空的附魔集合。
        ItemEnchantments itemenchantments = stack.getOrDefault(DataComponents.ENCHANTMENTS, ItemEnchantments.EMPTY);
        
        // 用于标记物品是否具有自动熔炼附魔。
        var isHaveEnchantment = false;
        
        // 遍历物品的所有附魔条目。
        for (Object2IntMap.Entry<Holder<Enchantment>> entry : itemenchantments.entrySet()) {
            // 获取附魔持有者和其对应的附魔值。
            Holder<Enchantment> enchantmentHolder = entry.getKey();
            Enchantment value = enchantmentHolder.value();
            
            // 获取与MY_AUTO_SMELT_TOOL组件相关联的所有条件效果。
            List<ConditionalEffect<EnchantmentValueEffect>> effects = value.getEffects(ModEnchantmentEffectComponents.MY_AUTO_SMELT_TOOL.value());
            
            /****
             *  假设你需要获得所有的effects,并对这些effect的数值进行计算,最后返回
             * 
                var returnValue = 0;
                for (ConditionalEffect<EnchantmentValueEffect> effect : effects) {
                    EnchantmentValueEffect effect1 = effect.effect();
                    var applyValue = effect1.process(entry.getIntValue(),level.random,returnValue);
                    returnValue += applyValue;
                }
                return  returnValue;
             * 
             * */

            // 如果存在效果，则说明物品具有自动熔炼附魔，更新标记并退出循环。
            if (!effects.isEmpty()) {
                isHaveEnchantment = true;
                break;
            }
        }
        // 返回最终的检查结果。
        return isHaveEnchantment;
    }
}
```

# 对于原版的附魔实现数值修改的逻辑

我们以原版的经验修补为例子讲解下原版的实现逻辑。


```java
    private int repairPlayerItems(ServerPlayer player, int value) {
        // 此行检查玩家是否有任何带有“XP修复”魔法的物品并损坏。它返回一个Optional对象，如果找不到此类项，则该对象可以为空。
        Optional<EnchantedItemInUse> optional = EnchantmentHelper.getRandomItemWith(EnchantmentEffectComponents.REPAIR_WITH_XP, player, ItemStack::isDamaged);
        if (optional.isPresent()) {
            ItemStack itemstack = optional.get().itemStack();//取出损坏的物品。
            //根据给定值和项目的修复率计算项目可以修复的程度。
            int i = EnchantmentHelper.modifyDurabilityToRepairFromXp(player.serverLevel(), itemstack, (int) (value * itemstack.getXpRepairRatio()));
            int j = Math.min(i, itemstack.getDamageValue());
            //考虑到物品当前的损坏情况，确定所需的实际维修量。
            itemstack.setDamageValue(itemstack.getDamageValue() - j);
            if (j > 0) {
                int k = value - j * value / i;
                if (k > 0) {
                    return this.repairPlayerItems(player, k);//通过减少损坏来修理该物品。
                }
            }

            return 0;
        } else {
            return value;
        }
    }

```


```java
// 该方法用于从LivingEntity的装备中随机获取一个符合特定条件（由componentType和filter定义）的附魔物品。
// 参数componentType指定所需的数据组件类型，entity表示LivingEntity对象，filter是一个用于筛选ItemStack的Predicate。
// 返回值是一个Optional对象，可能包含一个EnchantedItemInUse实例，表示找到的附魔物品及其使用情况。
public static Optional<EnchantedItemInUse> getRandomItemWith(DataComponentType<?> componentType, LivingEntity entity, Predicate<ItemStack> filter) {
    List<EnchantedItemInUse> list = new ArrayList<>();

    for (EquipmentSlot equipmentslot : EquipmentSlot.values()) {
        ItemStack itemstack = entity.getItemBySlot(equipmentslot);
        if (filter.test(itemstack)) {
            ItemEnchantments itemenchantments = itemstack.getOrDefault(DataComponents.ENCHANTMENTS, ItemEnchantments.EMPTY);

            for (Entry<Holder<Enchantment>> entry : itemenchantments.entrySet()) {
                Holder<Enchantment> holder = entry.getKey();
                if (holder.value().effects().has(componentType) && holder.value().matchingSlot(equipmentslot)) {
                    list.add(new EnchantedItemInUse(itemstack, equipmentslot, entity));
                }
            }
        }
    }

    return Util.getRandomSafe(list, entity.getRandom());
}



// 该方法用于根据经验值修改物品的耐久度修复值。
// 参数：level - 服务器等级，stack - 物品堆栈，duabilityToRepairFromXp - 初始耐久度修复值
// 返回值：修改后的耐久度修复值，最小为0
public static int modifyDurabilityToRepairFromXp(ServerLevel level, ItemStack stack, int duabilityToRepairFromXp) {
    MutableFloat mutablefloat = new MutableFloat((float)duabilityToRepairFromXp);
    // enchantment,enchantment level
    runIterationOnItem(stack, (p_344540_, p_344541_) -> p_344540_.value().modifyDurabilityToRepairFromXp(level, p_344541_, stack, mutablefloat));
    return Math.max(0, mutablefloat.intValue());
}

// 该方法用于在物品堆上运行迭代，对每个附魔进行处理
private static void runIterationOnItem(ItemStack stack, EnchantmentHelper.EnchantmentVisitor visitor) {
    ItemEnchantments itemenchantments = stack.getOrDefault(DataComponents.ENCHANTMENTS, ItemEnchantments.EMPTY);

    // Neo: Respect gameplay-only enchantments when doing iterations
    var lookup = net.neoforged.neoforge.common.CommonHooks.resolveLookup(net.minecraft.core.registries.Registries.ENCHANTMENT);
    if (lookup != null) {
        itemenchantments = stack.getAllEnchantments(lookup);
    }

    for (Entry<Holder<Enchantment>> entry : itemenchantments.entrySet()) {
        visitor.accept(entry.getKey(), entry.getIntValue());
    }
}

    // 定义一个函数式接口EnchantmentVisitor，该接口包含一个接受附魔和等级的方法
    @FunctionalInterface
    interface EnchantmentVisitor {
        void accept(Holder<Enchantment> enchantment, int level);
    }



// 该方法用于根据经验值修改工具的耐久度修复值
public void modifyDurabilityToRepairFromXp(ServerLevel level, int enchantmentLevel, ItemStack tool, MutableFloat durabilityToRepairFromXp) {
    this.modifyItemFilteredCount(EnchantmentEffectComponents.REPAIR_WITH_XP, level, enchantmentLevel, tool, durabilityToRepairFromXp);
}

// 修改物品过滤后的计数
private void modifyItemFilteredCount(
    DataComponentType<List<ConditionalEffect<EnchantmentValueEffect>>> componentType,
    ServerLevel level,
    int enchantmentLevel,
    ItemStack tool,
    MutableFloat value
) {
    applyEffects(
        this.getEffects(componentType),
        itemContext(level, enchantmentLevel, tool),
        p_347300_ -> value.setValue(p_347300_.process(enchantmentLevel, level.getRandom(), value.getValue())) // EnchantmentValueEffect
    );
}

// 该方法用于获取指定组件类型的效果列表。如果组件不存在，则返回一个空的列表。
public <T> List<T> getEffects(DataComponentType<List<T>> component) {
    return this.effects.getOrDefault(component, List.of());
}


// 应用条件效果的方法。遍历给定的条件效果列表，如果某个效果的条件匹配当前的战利品上下文，则应用该效果。
private static <T> void applyEffects(List<ConditionalEffect<T>> effects, LootContext context, Consumer<T> applier) {
        for (ConditionalEffect<T> conditionaleffect : effects) {
            if (conditionaleffect.matches(context)) {
                applier.accept(conditionaleffect.effect());
            }
        }
    }



```


# 实现自己的附魔物品的tag

我们在之前已经说了，一个附魔可以附魔在什么上面是通过tag指定的， 我们下面来看怎么创建自己的tag，对于数据包你只需要效仿原版的做法在/data/modid/tags/item/enchantable/目录下添加新的文件即可。这个文件应该包含你要附魔上的物品。例如稿子，斧子等。

如果是物品直接输入

```json
{
  "values": [
    "minecraft:bow"
  ]
}
```

如果是另一个tag的内容，可以使用#指定。

```json
{
  "values": [
    "#minecraft:axes",
    "#minecraft:pickaxes",
    "#minecraft:shovels",
    "#minecraft:hoes",
    "minecraft:shears"
  ]
}
```

在其他的地方使用直接使用 `#id:jsonname` 即可


对于数据生成。

我们创建一个item tags

```java
package com.example.examplemod.tags;


public class ModItemTags {
    public static final TagKey<Item> MY_MINING_ENCHANTABLE = bind("enchantable/my_mining_enchantable");
    private static TagKey<Item> bind(String name) {
        return TagKey.create(Registries.ITEM, ResourceLocation.fromNamespaceAndPath(ExampleMod.MODID,name));
    }
}
```

使用数据生成给我们的tags添加物品

```java
package com.example.examplemod.datagen.item.tags;


public class ModtemTagsProvider extends ItemTagsProvider {
    public ModtemTagsProvider(PackOutput output, CompletableFuture<HolderLookup.Provider> lookupProvider, CompletableFuture<TagLookup<Block>> blockTags) {
        super(output, lookupProvider, blockTags);
    }
    // 对应的物品，以及对应的tag
    @Override
    protected void addTags(HolderLookup.Provider provider) {
        this.tag(ModItemTags.MY_MINING_ENCHANTABLE).add(Items.DIAMOND_PICKAXE)
                .addTag(ItemTags.AXES);
    }
}

```

为了使用Item tag，我们还需要一个block tags，不过我们不添加内容。


```java
package com.example.examplemod.datagen.item.tags;

import java.util.concurrent.CompletableFuture;

public class ModBlockTagsProvider extends BlockTagsProvider {
    public ModBlockTagsProvider(PackOutput output, CompletableFuture<HolderLookup.Provider> lookupProvider, String modId, @Nullable ExistingFileHelper existingFileHelper) {
        super(output, lookupProvider, modId, existingFileHelper);
    }

    @Override
    protected void addTags(HolderLookup.Provider provider) {

    }
}

```

数据生成

```java
package com.example.examplemod.datagen;


// 用于注册数据生成器的类，该类通过Forge的EventBusSubscriber注解自动注册到MOD总线上
@EventBusSubscriber(modid = ExampleMod.MODID,bus = EventBusSubscriber.Bus.MOD)
public class ModDataGenerator {
    // 订阅GatherDataEvent事件，当数据收集事件触发时执行该方法
    @SubscribeEvent
    public static void gatherData(GatherDataEvent event) {
        DataGenerator generator = event.getGenerator();
        PackOutput output = generator.getPackOutput();
        CompletableFuture<HolderLookup.Provider> lookupProvider = event.getLookupProvider();
        ExistingFileHelper existingFileHelper = event.getExistingFileHelper();

        // 为数据生成器添加一个自定义的数据包内置条目提供者
        generator.addProvider(event.includeServer(),new ModDatapackBuiltinEntriesProvider(output,lookupProvider));

        TagsProvider<Block> blcokTags = generator.addProvider(event.includeServer(),new ModBlockTagsProvider(output,lookupProvider,ExampleMod.MODID,existingFileHelper));
        //
        generator.addProvider(event.includeServer(),new ModtemTagsProvider(output,lookupProvider,blcokTags.contentsGetter(),ExampleMod.MODID,existingFileHelper));
    }
}

```
