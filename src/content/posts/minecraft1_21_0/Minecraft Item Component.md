---
title: Neoforge 1.21 item Component教程
published: 2024-08-11
tags: [Minecraft1_21, Tutorial]
description: Neoforge Minecraft1.21 附魔教程
image: https://cdn.jsdelivr.net/gh/Flandre923/CDN@latest/img/20240811183140.png
category: Minecraft1_21_NeoForge_Tutorial
draft: false
---


# Minecraft Item Component
# Item

如何设置一个item上的DataComponentType的数值：

```java
// Item.properties

        public <T> Properties component(DataComponentType<T> component, T value) {
            CommonHooks.validateComponent(value);
            if (this.components == null) {
                this.components = DataComponentMap.builder().addAll(DataComponents.COMMON_ITEM_COMPONENTS);
            }

            this.components.set(component, value);
            return this;
        }
```

```java
// 同Properties的component设置添加DataComponentType的数值
public static final DeferredItem<Item> RUBY_STAFF=registerItem("ruby_staff",
            ()-> new RubyStuffItem(new Item.Properties().stacksTo(1).component(ModDataComponents.RUBY_STAFF_DES, List.of("123"))));
```

获得Item的components

```java
   public DataComponentMap components() {
        return this.components;
    }
```

# ItemStack

获得对应的ItemStack的下DataComponentType的对应的数值。

```java
        Tool tool = (Tool)stack.get(DataComponents.TOOL); // itemstack

```

判断是否是食物 判断是否有某个DataComponentType

```java
    public UseAnim getUseAnimation(ItemStack stack) {
        return stack.has(DataComponents.FOOD) ? UseAnim.EAT : UseAnim.NONE; // itemstack
    } // 通过判断是否有Food的DataComponentType类觉得是否是一个食物。

```

获得ItemStack所有的`components`​

```java
    public DataComponentMap getComponents() {
        return (DataComponentMap)(!this.isEmpty() ? this.components : DataComponentMap.EMPTY);
    }

```

获得Item的components

```java
    public DataComponentMap getPrototype() {
        return !this.isEmpty() ? this.getItem().components() : DataComponentMap.EMPTY;
    }

```

对于`DataComponentMap`​的设置方法

```java


    @Nullable
    public <T> T get(DataComponentType<? extends T> component) { // 通过component的获得对应的DataComponentType的数值
        Optional<? extends T> optional = (Optional)this.patch.get(component);
        return optional != null ? optional.orElse((Object)null) : this.prototype.get(component);
    }

    @Nullable
    public <T> T set(DataComponentType<? super T> component, @Nullable T value) { // 给对应的DataComponentType设置数值
        CommonHooks.validateComponent(value);
        this.ensureMapOwnership();
        T t = this.prototype.get(component);
        Optional optional;
        if (Objects.equals(value, t)) {
            optional = (Optional)this.patch.remove(component);
        } else {
            optional = (Optional)this.patch.put(component, Optional.ofNullable(value));
        }

        return optional != null ? optional.orElse(t) : t;
    }
```

‍

‍

# 注册和使用

好了我们来看下怎么注册一个自己的DataComponentType

注册DataComponentType的方法

```java

public class ModDataComponents {
    public static DeferredRegister<DataComponentType<?>> DATA_COMPONENTS = DeferredRegister.create(Registries.DATA_COMPONENT_TYPE, NeoMafishMod.MODID);


    public static final DeferredHolder<DataComponentType<?>,DataComponentType<List<String>>> RUBY_STAFF_DES = register(
            "ruby_staff_des", builder -> builder.persistent(Codec.STRING.listOf())
    );


// 注册一个自己的DataComponentType
    public static final DeferredHolder<DataComponentType<?>,DataComponentType<MyCustomData>> MY_CUSTOM_DATA = register(
            "my_custom_data", builder -> builder.persistent(MyCustomData.CODEC).networkSynchronized(MyCustomData.STREAM_CODEC)
    );

    private static <T> DeferredHolder<DataComponentType<?>,DataComponentType<T>> register(String name, UnaryOperator<DataComponentType.Builder<T>> builder) {
        return DATA_COMPONENTS.register(name,()->  builder.apply(DataComponentType.builder()).build());
    }

    public static void register(IEventBus eventBus){
        DATA_COMPONENTS.register(eventBus);
    }
}

```

```java

public record MyCustomData (int color, String name, int nutrition){

// codec 用于序列化和反序列化。
    public static Codec<MyCustomData> CODEC =
            RecordCodecBuilder.create(builder-> {
                return builder.group(Codec.INT.fieldOf("color").forGetter(MyCustomData::color),
                                Codec.STRING.fieldOf("name").forGetter(MyCustomData::name),
                                Codec.INT.fieldOf("nutrition").forGetter(MyCustomData::nutrition))
                        .apply(builder, MyCustomData::new);
            });

// stream codec 用于网络传输
    public static StreamCodec<RegistryFriendlyByteBuf,MyCustomData> STREAM_CODEC = new StreamCodec<RegistryFriendlyByteBuf, MyCustomData>() {
        @Override
        public MyCustomData decode(RegistryFriendlyByteBuf friendlyByteBuf) {
            return  new MyCustomData(friendlyByteBuf.readInt(),friendlyByteBuf.readUtf(),friendlyByteBuf.readInt());
        }

        @Override
        public void encode(RegistryFriendlyByteBuf o, MyCustomData myCustomData) {
            o.writeInt(myCustomData.color());
            o.writeUtf(myCustomData.name());
            o.writeInt(myCustomData.nutrition());
        }
    };

}

```

通过item properties 添加我们的DataComponentType对应的数值，以及获得对应的DataComponentType对应的数据。

‍

```java

public class ExampleComponent extends Item {

    public ExampleComponent() {
        super(new Properties().component(ModDataComponents.MY_CUSTOM_DATA,new MyCustomData(123,"123",123)));
    }


    @Override
    public InteractionResultHolder<ItemStack> use(Level level, Player player, InteractionHand usedHand) {
        DataComponentMap components = this.components();
        MyCustomData myCustomData = components.get(ModDataComponents.MY_CUSTOM_DATA.get());

        Logger logger = Logger.getLogger("ExampleComponent");

        if (myCustomData != null) {
            int  color = myCustomData.color();
            String name = myCustomData.name();
            int nutrition = myCustomData.nutrition();

            // 检查 color 和 name 是否为 null
            // 假设 logger.warning 只接受一个 String 参数
            String message = "Color: " + color + ", Name: " + name + ", Nutrition: " + nutrition;
            logger.warning(message);
        } else {
            // 如果 myCustomData 为 null，则记录错误信息
            logger.warning("myCustomData is null");
        }


        return super.use(level, player, usedHand);
    }
}

```

打印输出了

```log
8月 11, 2024 6:10:39 下午 com.mafuyu33.neomafishmod.item.custom.ExampleComponent use
警告: Color: 123, Name: 123, Nutrition: 123
8月 11, 2024 6:10:40 下午 com.mafuyu33.neomafishmod.item.custom.ExampleComponent use
警告: Color: 123, Name: 123, Nutrition: 123
```

# 代码
仓库代码：https://github.com/Flandre923/NeoForgeTutorialmod-Minecraft1.21/tree/itemComponent
