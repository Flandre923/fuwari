---
title: 08 药水效果和药水和Mixin
published: 2024-08-24
tags: ['Minecraft1_21', 'Tutorial']
description: Neoforge Minecraft1.21 mafishmod 模组涉及的物品教程
image: https://cdn.jsdelivr.net/gh/Flandre923/CDN/img/566dfff1-64eb-11ef-ac61-b81ea485754c.jpg
category: Minecraft1_21_NeoForge_Tutorial
draft: false
---


# 08 药水效果和药水和Mixin

药水效果注册，你需要的是一个药水效果的名称以及一个对应的实例的supplier。这个实例可以直接new的方式.

```java

public class ModEffects {
	// 获得药水效果的注册器
    public static  DeferredRegister<MobEffect> EFFECTS = DeferredRegister.create(Registries.MOB_EFFECT, NeoMafishMod.MODID);
	// 注册了几个药水效果
    public static final DeferredHolder<MobEffect,MobEffect> IRONMAN =registerDeferredHolder("ironman",()->new NormalEffect(MobEffectCategory.BENEFICIAL,0xFF0000)) ;
    public static final DeferredHolder<MobEffect,MobEffect>  FLOWER_EFFECT =registerDeferredHolder("flower_effect",()->new NormalEffect(MobEffectCategory.BENEFICIAL,0xFF0000)) ;
    public static final DeferredHolder<MobEffect,MobEffect>  TELEPORT_EFFECT = registerDeferredHolder("teleport_effect",()->new NormalEffect(MobEffectCategory.BENEFICIAL,0x00FFFF));
    public static final DeferredHolder<MobEffect,MobEffect>  SPIDER_EFFECT = registerDeferredHolder("spider_effect",()->new NormalEffect(MobEffectCategory.BENEFICIAL,0x800000));
    public static final DeferredHolder<MobEffect,MobEffect>  SHEEP_EFFECT = registerDeferredHolder("sheep_effect",()->new NormalEffect(MobEffectCategory.BENEFICIAL,0x80F18BEB));
    public static final DeferredHolder<MobEffect,MobEffect>  ANTIDOTE_EFFECT =registerDeferredHolder("antidote_effect",()->new NormalEffect(MobEffectCategory.BENEFICIAL,0x80FFFFFF));

	// 辅助的注册的方法
    public static DeferredHolder<MobEffect,MobEffect> registerDeferredHolder(String name, Supplier<MobEffect> supplier){
        return EFFECTS.register(name,supplier);
    }
	// 将使用的的注册器添加到总线
    public static void register(IEventBus eventBus){
        EFFECTS.register(eventBus);
    }
}

```

先看下注册到总线的代码

```java
// The value here should match an entry in the META-INF/neoforge.mods.toml file
@Mod(NeoMafishMod.MODID)
public class NeoMafishMod
{
    // Define mod id in a common place for everything to reference
    public static final String MODID = "neomafishmod";
    private static final Logger LOGGER = LogUtils.getLogger();
    public NeoMafishMod(IEventBus modEventBus, ModContainer modContainer)
    {
        ModDataComponents.register(modEventBus);
        ModItems.ITEMS.register(modEventBus);
        ModTabs.CREATIVE_TABS.register(modEventBus);
        ModBlock.register(modEventBus);
        ModEntities.register(modEventBus);
        ModSounds.register(modEventBus);
        ModEffects.register(modEventBus); // 药水效果的注册到总线
        ModPotions.register(modEventBus); // 药水注册到总线, 等会不在重复.

        modContainer.registerConfig(ModConfig.Type.COMMON,Config.SPEC);
    }
}
```

我们来看`NormalEffect`​类,这个`NormalEffect`​类,并没有做什么,只是继承了`MobEffect`​

```java
// 作为我们新建的药水效果的一个标签
public class NormalEffect extends MobEffect {
    public NormalEffect(MobEffectCategory category, int color) {
        super(category, color);
    }
}

```

‍

注册对应的药水,需要给出的是对应的name.以及持续时间和药水的等级.最后一个是药水的效果.

```java
public class ModPotions {
    public static DeferredRegister<Potion> POTIONS = DeferredRegister.create(Registries.POTION, NeoMafishMod.MODID);

    public static Holder<Potion> FLOWER_POTION = registerPotion("flower_potion",3600,5, ModEffects.FLOWER_EFFECT);
    public static Holder<Potion>  TELEPORT_POTION = registerPotion("teleport_potion",100,0,ModEffects.TELEPORT_EFFECT);
    public static Holder<Potion>  SPIDER_POTION =  registerPotion("spider_potion",3600,0,ModEffects.SPIDER_EFFECT);
    public static Holder<Potion>  SHEEP_POTION = registerPotion("sheep_potion",2000,0,ModEffects.SHEEP_EFFECT);
    public static Holder<Potion>  ANTIDOTE_POTION = registerPotion("antidote_potion",2000,0,ModEffects.ANTIDOTE_EFFECT);

    public static void register(IEventBus eventBus){
        POTIONS.register(eventBus);
    }
// 
    public static DeferredHolder<Potion, Potion> registerPotion(String name, int duration, int amplifier, Holder<MobEffect> statusEffects) {
        return POTIONS.register(name,()-> new Potion(new MobEffectInstance(statusEffects,duration,amplifier)));
    }


}

```

然后我们说下怎么给对应的药水添加合成表,这个功能可以通过neoforge提供的事件来添加

```java

@EventBusSubscriber(modid = NeoMafishMod.MODID)
public class registerPotionsBrewingEvent {

    @SubscribeEvent
    public static void onRegisterPotionsBrewing(RegisterBrewingRecipesEvent event){ // 注册药水的合成表的事件
        PotionBrewing.Builder builder = event.getBuilder();

        // 传送药水
        builder.addMix(Potions.AWKWARD, // 使用的药水,使用的材料,获得的药水
                Items.ENDER_PEARL, ModPotions.TELEPORT_POTION);
        //蜘蛛药水
        builder.addMix(Potions.AWKWARD,
                Items.FERMENTED_SPIDER_EYE, ModPotions.SPIDER_POTION);
        //变形魔药
        builder.addMix(Potions.AWKWARD,
                Items.GOAT_HORN, ModPotions.SHEEP_POTION);
        //百毒不侵
        builder.addMix(Potions.AWKWARD,
                Items.MILK_BUCKET, ModPotions.ANTIDOTE_POTION);

    }
}

```

我们再来看药水效果怎么生效,除了一些Mobeffect本省提供的方法,你可以通过这些方法,来是实现功能,例如原版的生命恢复等药水效果,你可以自己去参考,另一部的药水效果需要你在需要生效的位置编写对应的代码逻辑才能生效,例如原版的力量,力量的生效需要你指定的攻击代码中编写对应的逻辑.

就例如这里的传送的药水效果,这个药水的效果是拥有这个效果后就会在附近随机一个位置传送

```java
    public static final DeferredHolder<MobEffect,MobEffect>  TELEPORT_EFFECT = registerDeferredHolder("teleport_effect",()->new NormalEffect(MobEffectCategory.BENEFICIAL,0x00FFFF));

```

我们这里说一下mixin是什么,就是提供了一些方法,让你通过这些方法去添加修改MC的原方法,修改原方法的逻辑,或者添加新的逻辑.

neoforge使用mixin很方便只需要将neoforge.mods.toml下mixin的注解打开,然后添加一个resources/neomafishmod.mixins.json对应的json文件,将对应的neomafishmod改为你自己的modid

```json
{
  "required": true,
  "package": "com.mafuyu33.neomafishmod.mixin", // 你需要修改此位置,保证你的mixin相关的代码存放的位置在该位置
  "compatibilityLevel": "JAVA_21",
  "mixins": [
  ],
  "client": [

  ],
  "injectors": {
    "defaultRequire": 1
  }
}
```

​![image](https://cdn.jsdelivr.net/gh/Flandre923/CDN/img/329a748d-64ea-11ef-85df-b81ea485754c.png)​

此后如果你想让你的mxin生效的话,那么你就需要将对应的类添加到其中

​![image](https://cdn.jsdelivr.net/gh/Flandre923/CDN/img/33628dd5-64ea-11ef-a199-b81ea485754c.png)​

如果你的mixin的类仅仅在客户端生效,那么就应该选择该方法

​![image](https://cdn.jsdelivr.net/gh/Flandre923/CDN/img/341127ef-64ea-11ef-8a56-b81ea485754c.png)​

‍

这里推荐一个插件叫做minecraft development的插件

​![image](https://cdn.jsdelivr.net/gh/Flandre923/CDN/img/34b0f63f-64ea-11ef-acb1-b81ea485754c.png)​

在marketplace中下载即可,然后这个插件可以帮你补全mixin,并且可以提示当前的mixin是否添加到了对应的json文件中.

在你配置好了mixin之后你需要重新reload项目使得其功能生效

‍

为了使得我们药水效果生效,需要在指定的位置编写代码,这里我们使用的是livingentity的tick方法,这个tick方法就是每个游戏的tick刻会进行回调的方法.

```java
// 我们使用的mixin,mixin进入了LivingEntity
@Mixin(LivingEntity.class)
public abstract class TeleportEffectMixin extends Entity implements Attackable {
    @Shadow public abstract boolean hasEffect(Holder<MobEffect> effect); // 我们需要使用到这个方法,使用shadow能让你直接访问到LivingEntity下面的方法.

    @Unique
    int time = 0; // 这里我们定义了自己的time字段,unique表示该字段是我们自己写的,不是原来的类.

    public TeleportEffectMixin(EntityType<?> entityType, Level level) {
        super(entityType, level);
    }


    @Override
    public boolean canBeCollidedWith() {
        return ColliableItem.isColliable(); 
    }

    @Inject(method = "tick", at = @At("HEAD"))
    private void init(CallbackInfo ci) {
        if(this.isAlwaysTicking() && this.hasEffect(ModEffects.TELEPORT_EFFECT)) {//传送药水
            time++;
            if (time > 1) { // 这里我们查看是否是一个合法的计时,如果是我们就随机传送我们的药水效果的拥有者.
                randomTeleport(this.level(), (LivingEntity) (Object) this);// 这里展示了一个如何获得对应的mixin类的this指针的方法.
                time=0; // 重置计时器
            }
        }
    }


    @Unique
    private void randomTeleport(Level world, LivingEntity user) { // 这个方法就是随机传送的方法,unique表示的意思和上述的一致.
        if (!world.isClientSide) {
            for(int i = 0; i < 16; ++i) {
                double d = user.getX() + (user.getRandom().nextDouble() - 0.5) * 16.0;
                double e = Math.clamp(user.getY() + (double)(user.getRandom().nextInt(16) - 8), (double)world.getMinBuildHeight(), (double)(world.getMinBuildHeight() + ((ServerLevel)world).getLogicalHeight() - 1));
                double f = user.getZ() + (user.getRandom().nextDouble() - 0.5) * 16.0;
                if (user.isPassenger()) {
                    user.stopRiding();
                }

                Vec3 vec3d = user.position();
                if (user.randomTeleport(d, e, f, true)) {
                    world.gameEvent(GameEvent.TELEPORT, vec3d, GameEvent.Context.of(user));
                    SoundSource soundCategory;
                    SoundEvent soundEvent;
                    if (user instanceof Fox) {
                        soundEvent = SoundEvents.FOX_TELEPORT;
                        soundCategory = SoundSource.NEUTRAL;
                    } else {
                        soundEvent = SoundEvents.CHORUS_FRUIT_TELEPORT;
                        soundCategory = SoundSource.PLAYERS;
                    }

                    world.playSound((Player) null, user.getX(), user.getY(), user.getZ(), soundEvent, soundCategory);
                    user.resetFallDistance();
                    break;
                }
            }
        }
    }

}

```

记得在对对应的json中添加

​![image](https://cdn.jsdelivr.net/gh/Flandre923/CDN/img/35865459-64ea-11ef-9217-b81ea485754c.png)​

好了,到此为止,我们说了怎么添加效果,怎么添加药水,怎么添加合成表,以及使得对应的效果生效.

‍