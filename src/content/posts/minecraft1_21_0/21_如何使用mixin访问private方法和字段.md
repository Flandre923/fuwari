---
title: 21 如何使用mixin访问private方法和字段
published: 2024-08-24
tags: ['Minecraft1_21', 'Tutorial']
description: Neoforge Minecraft1.21 mafishmod 模组涉及的物品教程
image: https://cdn.jsdelivr.net/gh/Flandre923/CDN/img/43ed72ae-64ea-11ef-9b2b-b81ea485754c.jpg
category: Minecraft1_21_NeoForge_Tutorial
draft: false
---


# 21 如何使用mixin访问private方法和字段

我们现在想要访问WalkAnimationState下的一些字段,但是这些字段是private, 我们可以通过accessor的方法访问.或者通过set设置.

```java

@Mixin(WalkAnimationState.class)
public  interface LimbAnimatorAccessor {


    @Accessor("speedOld")
    float getSpeedOld();

    @Accessor("speedOld")
    void setSpeedOld(float speedOld);

    @Accessor
    float getSpeed();

    @Accessor
    void setSpeed(float speed);

    @Accessor
    float getPosition();

    @Accessor
    void setPosition(float pos);


}

```

​![image](https://cdn.jsdelivr.net/gh/Flandre923/CDN/img/47dfe17c-64ea-11ef-a237-b81ea485754c.png)​

通过accessor,写好对应的设置好对应的方法名称可以自动识别到对应的属性.

也可以通过accessor指定speedOld数值.

```java
  @Accessor("speedOld")
    float getSpeedOld();

    @Accessor("speedOld")
    void setSpeedOld(float speedOld);

```

‍

```java
// 通过获得对应的类,将其转化为我们写的接口,然后就可以使用这些方法了.
            LimbAnimatorAccessor target = (LimbAnimatorAccessor) sheep.walkAnimation;
            LimbAnimatorAccessor source = (LimbAnimatorAccessor) entity.walkAnimation;
            target.setSpeedOld(source.getSpeedOld());
            target.setSpeed(source.getSpeed());
            target.setPosition(source.getPosition());
```

‍

访问对应的private 的方法

```java

@Mixin(BlockEntity.class)
public interface EntityBlockAccessor {

    @Invoker("saveAdditional") // saveAdditional 指定了blockentity.class的save方法.
    void saveAdditional(CompoundTag tag, HolderLookup.Provider registries);

}

```

​![image](https://cdn.jsdelivr.net/gh/Flandre923/CDN/img/4867da56-64ea-11ef-a342-b81ea485754c.png)​

‍