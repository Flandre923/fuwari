---
title: NeoForge 20.5 for Minecraft 1.20.5
published: 2024-04-28
description: "NeoForge 20.5 for Minecraft 1.20.5"
image: "https://cdn.jsdelivr.net/gh/Flandre923/CDN@latest/img/wallhaven-yx3l9x.jpg"
tags: ["NeoForge_new", "Minecraft", "Neoforge"]
category: NeoForge_News
draft: false
---

# 注意
本文是对neoforge文章的翻译，可能存在错误或者不合适的地方，机翻。仅供参考
原文地址：https://neoforged.net/news/20.5release/
侵删
# Java21
Minecraft 1.20.5 使用了Java21版本。,现在你需要升级的你的Java到21版本了。

一种升级方案是修改你的buildgradle文件。

```diff
- java.toolchain.languageVersion = JavaLanguageVersion.of(17)
+ java.toolchain.languageVersion = JavaLanguageVersion.of(21)
```
NeoGradle版本：7.0.105
Gradle版本8.6

# neoforge.mods.toml文件
从20.5之后，模组的唯一标识文件是 neoforge.mods.toml，仍然位于 META-INF文件夹下，所有的 mods.toml文件应该改名为neoforge.mods.toml，才能被识别。这一更改是为了区别neoforge和forge模组。

记得还需要在Gradle Script中修改mods.toml为 neoforge.mods.toml，特别是processResources 内容中。

从这篇这篇文章开始，没有neoforge.mods.toml的文件将会被跳过，不过之后会有更好的提醒方式。

# Tag约定更新

通用标签：现在，所有模组中通用的标签都应该使用c命名空间，这里的c代表“通用”（common）或“约定”（convention）。

层级结构：相关的标签组被鼓励使用层级结构来组织。比如，所有的矿石可以用c:ores来标记，而所有的锡矿石可以用c:ores/tin来进一步细分。

模组开发者可以通过Tags类来访问这些标签的定义。这个类是NeoForge提供的一个工具，它包含了所有标签的详细信息，帮助开发者在创建模组时使用正确的标签。

数据包作者：为Minecraft创建自定义内容包（datapack）的作者现在也可以使用这种新的标签格式，以确保他们的数据包能够与模组兼容。

多平台模组开发者：同时在Fabric（另一个Minecraft模组加载器）和其他平台上开发模组的开发者可以放心地使用这种新的标签格式，因为它已经被Fabric社区接受，并且被认为是安全的。

# Item 数据组件
ItemStacks的数据存储方式发生了变化。

以前，每个ItemStack都有一个叫做CompoundTag的东西来存储所有的数据，比如物品的数量、耐久度等。但现在，这种方式被取消了。取而代之的是，一个ItemStack可以有任意数量的数据组件。每个数据组件都是一个简单的Java对象，并且由一个DataComponentType来标识。

这意味着，如果游戏的修改者（Modders）想要在物品堆上存储自己的数据，他们应该创建并注册自己的DataComponentTypes。游戏原版（Vanilla）的组件类型可以在DataComponentTypes类中找到。

> WARNING: ItemStack的复制是浅复制，这意味着复制的两个堆共享相同的组件。在比较两个堆是否相等时，会使用组件的equals方法，而在对堆进行哈希操作时，可能会调用组件的hashCode方法。因此，数据组件必须是不可变的，并且需要正确实现equals和hashCode方法。NeoForge添加了一个合理性检查，以确保组件覆盖了这些方法。

例如，假设我们想在一个ItemStack上存储一个整数值，代码将会如下更改：

```java

// 新的数据组件类型，用于存储能量值
DataComponentType<Integer> ENERGY = ...;

// 获取能量值的旧方法和新方法
- int energy = stack.getOrCreateTag().getInt("energy");
+ int energy = stack.getOrDefault(ENERGY, 0);

// 设置能量值的旧方法和新方法
- stack.getOrCreateTag().setInt("energy", 10);
+ stack.set(ENERGY, 10);
```

为了方便参考，以下是一些相关的代码部分：

`DataComponentType` 和 `DataComponentType.Builder`：用于创建和构建新的数据组件类型。
- `DataComponentHolder`：由itemstack实现，用于持有数据组件。
- `ItemStack.set(…, …)` 和 `ItemStack.remove(…)`：用于在itemstack上添加或移除数据组件。
- `ItemStack.update(…)`：当需要更新组件的值时提供便利。

此外，物品（Items）可以提供一组默认的组件。

查看 `Item.Properties.component(…)` 来为物品添加默认组件。

## 和数据附件（Data Attachments）的比较
**数据附件（Data Attachments）** 已经被移除了，因为它们已经被新的 **数据组件（Data Components）** 系统取代。在旧系统中，数据附件是可变的，并且每个itemstack（Stack）都有独特的附件。但是，在新系统中，数据组件是在多个物品堆之间共享的，并且必须是不可变的。

这个变化的原因是，数据组件提供了一种更加安全和高效的方式来存储和管理数据。因为组件是不可变的，所以它们在多个物品堆之间共享时不会出现数据冲突或不一致的问题。这使得物品堆的比较和哈希操作更加可靠。

尽管如此，目前数据附件在其他类型的对象上仍然是完全功能性的，这包括方块实体（Block Entities）、区块（Chunks）、实体（Entities）和世界（Levels）。这意味着，虽然物品堆不再使用数据附件，但在游戏的其他部分，数据附件仍然是一个有效的数据存储选项。

# 网络更新

在这次的网络更新中，我们引入了一种新的编解码器：StreamCodecs。这个更新还包括了对自定义负载的更多改变。

Stream Codecs（流编解码器） 流编解码器用于抽象化网络缓冲区的数据读写过程，这与使用编解码器抽象化JSON或NBT的数据读写类似，但实现更为简单。流编解码器有两种泛型类型：缓冲类型和数据类型。常见的缓冲类型包括：

- ByteBuf：用于最通用的流编解码器。
- FriendlyByteBuf：用于那些利用FriendlyByteBuf中的便捷方法的编解码器，或者依赖于使用FriendlyByteBuf的其他编解码器。
- RegistryFriendlyByteBuf：用于访问注册信息的编解码器，如原始整数ID。
例如，一个读取整数的流编解码器将具有类型StreamCodec<ByteBuf, Integer>，而一个读取物品堆栈的流编解码器将具有类型StreamCodec<RegistryFriendlyByteBuf, ItemStack>。

流编解码器被设计为可以使用组合器组合在一起。以下是一些入门指南：

- ByteBufCodecs 包含许多有用的流编解码器，例如ByteBufCodecs.INT和其他原始类型。
- StreamCodec.composite(...) 用于组合多个流编解码器，例如用于类的字段。
- 列表流编解码器是使用streamCodec.apply(ByteBufCodecs.list())创建的。其他集合也存在类似的方法。
- NeoForge 在其NeoForgeStreamCodecs类中提供了额外的帮助。
这些更新旨在简化网络数据的处理，并提高开发者在创建自定义网络功能时的灵活性和效率。

## 自定义Payload改变

在NeoForge的最新更新中，我们对自定义负载进行了一些重要的改变。现在，CustomPacketPayloads（自定义数据包负载）通过一个围绕ResourceLocation（资源定位符）的CustomPacketPayload.Type（自定义数据包负载类型）包装器来识别。此外，序列化和反序列化过程由StreamCodec（流编解码器）处理。关于流编解码器的第一个泛型参数，请注意：

- RegistryFriendlyByteBuf 仅在游戏数据包中可用。
- FriendlyByteBuf 可用于任何数据包。
- 如果不需要FriendlyByteBuf的便捷方法，ByteBuf 也可以用于任何数据包。

数据包类型和流编解码器必须使用RegisterPayloadHandlersEvent（注册负载处理器事件）事件进行注册。

这些变化意味着开发者现在可以更加精确地控制数据包的识别和数据的处理方式，从而提高网络通信的效率和可靠性。

> 每个数据包就像一个有着特定目的地的快递包裹。CustomPacketPayload.Type就像是包裹上的标签，告诉我们这个包裹应该送到哪里。StreamCodec则像是处理包裹内容的工人，确保每件物品都能安全地打包和拆包。这次更新就是为了让这个过程更加高效和无误差。

## 网络API更新

在NeoForge的最新版本中，我们根据用户的反馈对网络API进行了重新组织，使其更加一致和易于使用。以下是变更的简要总结：

- 负载处理器默认在主线程上运行。这可以通过使用registrar.executesOn(HandlerThread.NETWORK)来更改。
  - IPayloadContext已被简化为单一接口，并新增了一些方法。
  - PayloadRegistrar中的注册方法已经被重命名。
  - RegisterPayloadHandlerEvent -> RegisterPayloadHandlersEvent：已重命名。
  - OnGameConfigurationEvent -> RegisterConfigurationTasksEvent：已重命名。
  - IPayloadRegistrar -> PayloadRegistrar：已被直接类引用替代。
  - isConnected -> hasChannel：已重命名。
- 我们还简化了用于发送数据包的PacketDistributor系统。更多详情请参考PacketDistributor类。

## 更新的例子

这里有一个更新的例子，它涉及到一个包含两个itemstack的自定义游戏CustomPacketPayload。

```java
public record TwoStacksPayload(ItemStack first, ItemStack second) implements CustomPacketPayload {
    // 我们有一个封装资源定位符的类型。
    public static final Type<TwoStacksPayload> TYPE = new Type<>(new ResourceLocation("mymod", "two_stacks"));

    // 我们还有一个流编解码器，这里使用RFBB (RegistryFriendlyByteBuf)，因为物品堆栈需要它。
    public static final StreamCodec<RegistryFriendlyByteBuf, TwoStacksPayload> STREAM_CODEC = StreamCodec.composite(
            ItemStack.OPTIONAL_STREAM_CODEC,
            TwoStacksPayload::first,
            ItemStack.OPTIONAL_STREAM_CODEC,
            TwoStacksPayload::second,
            TwoStacksPayload::new);

    @Override
    public Type<TwoStacksPayload> type() {
        return TYPE;
    }
}
```
接下来，我们来看看注册代码是怎样的：


```java
public static void onPayloadRegister(RegisterPayloadHandlersEvent event) { // 注意：Handlers是复数形式
    PayloadRegistrar registrar = event.registrar("version");

    // 也可以使用playToClient或playToServer来发送单向数据包
    registrar.playBidirectional(TwoStacksPayload.TYPE, TwoStacksPayload.STREAM_CODEC, <packet handler>);
    // 等等...
}
```

# DFU 更新
DFU库从6版更新到了7版，这次更新解决了DFU许多长期存在的问题，但也带来了一些重大变化。

以下是一些亮点：
- ExtraCodecs中的许多方法被移除了，因为DFU本身现在有了等效的方法。
  - ExtraCodecs.strictOptionalField(codec, ...) 被 codec.optionalFieldOf(...) 替代，现在默认是严格的。
  - ExtraCodecs.validate(codec, ...) 被 codec.validate(...) 替代。
  - ExtraCodecs.lazyInitializedCodec(() -> ...) 被 Codec.lazilyInitialized(() -> ...) 替代。
  - 等等…
- 依赖于类型字段的序列化的编解码器（通常称为分派编解码器）现在需要一个MapCodec来处理分派的类型。例如，配方序列化器现在返回一个MapCodec而不是Codec。
  - 使用RecordCodecBuilder时，应使用RecordCodecBuilder.mapCodec而不是.create来生成MapCodec。
  - 许多组合器，如.xmap，也将与MapCodecs一起工作。
  - 将MapCodec转换为Codec是通过mapCodec.codec()完成的。
  - Util.getOrThrow(dataResult, ...) 被 dataResult.getOrThrow(...) 替代。

# 其他小更新

## 事件系统

- @Mod.EventBusSubscriber -> @EventBusSubscriber：移动到顶层。
- Mod.EventBusSubscriber.Bus.FORGE -> EventBusSubscriber.Bus.GAME：常量重命名。游戏总线本身仍然位于NeoForge.EVENT_BUS。

## 游戏对象序列化

- 许多序列化方法现在需要一个额外的HolderLookup.Provider registries参数。
  - 它可以用于将编解码器序列化到NBT：registries.buildSerializationContext(NbtOps.INSTANCE)。
  - 注册表上下文可以从RegistryFriendlyByteBuf中检索，或用于创建一个。
- BlockEntity.save(CompoundTag tag) -> BlockEntity.saveAdditional(CompoundTag tag, HolderLookup.Provider registries)。
- 使用ItemStack.save(registries)和ItemStack.parseOptional(registries, compoundTag)来保存和加载物品堆栈到/从NBT。
  - 警告：ItemStack.save(registries, Tag)不会修改传递的标签。确保您始终使用返回的标签，或者更好地调用只需要registries的重载方法。

## GUI Layer层

RegisterGuiOverlaysEvent -> RegisterGuiLayersEvent。
VanillaGuiOverlay -> VanillaGuiLayers。

## 迁移指南

更多的变化被收集在以下的模组迁移指南中。感谢@ChampionAsh5357的撰写。

https://gist.github.com/ChampionAsh5357/53b04132e292aa12638d339abfabf955

# 1.20.1和1.20.4计划
我们将把重点放在1.20.5版本的开发上，但这并不意味着我们会放弃对1.20.1和1.20.4版本的支持。像往常一样，任何针对旧版本的Pull Request（代码合并请求）都需要首先被接受，并合并到1.20.5分支中。

