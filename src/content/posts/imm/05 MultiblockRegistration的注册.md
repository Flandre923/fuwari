---
title: 沉浸工艺多方块05 MultiblockRegistration注册
published: 2024-08-10
description: "沉浸工艺多方块"
image: "./assets/image.png"
tags: ["我的世界", "沉浸工艺"]
category: 沉浸工艺
draft: false
---
# 05 MultiblockRegistration的注册

还记得在注册多方块结构的时候的参数吗

```java
// 粉碎机
	public CrusherMultiblock()
	{
		super(new ResourceLocation(ImmersiveEngineering.MODID, "multiblocks/crusher"),
				CrusherLogic.MASTER_OFFSET, new BlockPos(2, 1, 2), new BlockPos(5, 3, 3),
				IEMultiblockLogic.CRUSHER);
	}
```

其中 第一个参数是标识符，第二个是master方块的位置，第三个是触发方块的位置，以及范围大小。而最后一个参数是`MultiblockRegistration`​

```java

public record MultiblockRegistration<State extends IMultiblockState>(
		IMultiblockLogic<State> logic,
		List<ExtraComponent<State, ?>> extraComponents,
		Supplier<BlockEntityType<? extends MultiblockBlockEntityMaster<State>>> masterBE,
		Supplier<BlockEntityType<? extends MultiblockBlockEntityDummy<State>>> dummyBE,
		Supplier<? extends MultiblockPartBlock<State>> block,
		Supplier<? extends Item> blockItem,
		boolean mirrorable,
		boolean hasComparatorOutput,
		boolean redstoneInputAware,
		boolean postProcessesShape,
		Supplier<BlockPos> getMasterPosInMB,
		Function<Level, Vec3i> getSize,
		Disassembler disassemble,
		Function<Level, List<StructureBlockInfo>> getStructure,
		ResourceLocation id
)
```

​`MultiblockRegistration`​是机器的一个存储数据的类，存储了机器的数据，包括数据的处理逻辑Logic，以及额外的一些组件，获得master方块实体的方法，和dummy方块实体的方法，对应的机器方块，对应的物品，以及是否镜像，是否处理比较器，是否处理红石信号输入。以及是否有后处理形状（指高卢追加鼓风机这种），在多方快结构中master方块的位置，以及size，当机器破坏时候的处理器。获得structure方法。和id。

​`MultiblockRegistration`​的构建可以通过一个builder来构建，

```java

	private static <S extends IMultiblockState>
	IEMultiblockBuilder<S> stone(IMultiblockLogic<S> logic, String name, boolean solid)
	{
		Properties properties = Properties.of() // 创建方块属性对象
				.sound(SoundType.NETHER_BRICKS)
				.mapColor(MapColor.STONE)
				.instrument(NoteBlockInstrument.BASEDRUM)
				.forceSolidOn() // 强制方块为实心
				.strength(2, 20);  // 设置方块抗爆和抗破坏等级 
		if(!solid)
			properties.noOcclusion();
		else
			properties.forceSolidOn(); // 设置方块的属性
		return new IEMultiblockBuilder<>(logic, name)// 创建多方块构建器
				.notMirrored()
				.customBlock( // 自定义方块
						BLOCK_REGISTER, ITEM_REGISTER, // 方块 物品注册表
						r -> new NonMirrorableWithActiveBlock<>(properties, r), // 方块的注册方法
						MultiblockItem::new // 物品的注册方法
				)// 创建方块实例, 创建物品实例
				.defaultBEs(BE_REGISTER);// 设置默认方块实体
	}
```

代码中提供了两个方法构建对应的`MultiblockRegistration`​，第一个stone，第二个metal，两个方法对应了机器方块是金属等级还是石头等级。我们用石头等级做例子。

```java
	public static final MultiblockRegistration<CokeOvenLogic.State> COKE_OVEN = stone(new CokeOvenLogic(), "coke_oven", true).structure(() -> IEMultiblocks.COKE_OVEN)
			.gui(IEMenuTypes.COKE_OVEN)
			.build();
```

可以通过 .的方式继续设置一些组件，例如这里设置了gui。

```java
	public IEMultiblockBuilder<S> gui(MultiblockContainer<S, ?> menu)
	{
		return component(new MultiblockGui<>(menu));//设置多方块结构的 GUI 容器。
	}
```

添加了一个gui的组件。

这样机器就可以处理gui了，当然对应的meau和screen还是需要你去添加的，这不是我们要讲解的内容，关于这一部分，大家可以看之前的教程。

```java

	public Self defaultBEs(RegistrationMethod<BlockEntityType<?>> register)
	{
		Preconditions.checkState(this.masterBE==null);
		Preconditions.checkState(this.dummyBE==null);
		//使用 register 方法注册主方块实体和哑巴方块实体，并将其赋值给 masterBE 和 dummyBE。
		this.masterBE = register.register(name.getPath()+MASTER_BE_SUFFIX, () -> makeBEType(MultiblockBlockEntityMaster::new));
		this.dummyBE = register.register(name.getPath()+DUMMY_BE_SUFFIX, () -> makeBEType(MultiblockBlockEntityDummy::new));
		return self();
	}
```

这里进行了方块实体的注册,其实是将对应的方块实体的构造方法传递给的masterBE,存储的是对应的构造方法.

```java

	public Self customBlock(
			RegistrationMethod<Block> register,
			RegistrationMethod<Item> blockItemRegister,
			Function<MultiblockRegistration<State>, ? extends MultiblockPartBlock<State>> make,
			Function<Block, Item> makeItem
	)
	{
		Preconditions.checkState(this.block==null);
		this.block = register.register(name.getPath(), () -> make.apply(this.result));
		this.item = blockItemRegister.register(name.getPath(), () -> makeItem.apply(this.result.block().get()));
		return self();
	}
```

这里进行了机器方块的物品和方块的注册。

这里封装的比较多，其实就是我们平常的注册，不过这里吧各种构造方法和对应的注册表穿了过来，进行的注册。

通过通过build就可以得到<u>​`MultiblockRegistration`​</u>​。

```java

	public <CS, C extends IMultiblockComponent<CS> & StateWrapper<State, CS>>
	Self selfWrappingComponent(C extraComponent)
	{ // 用于添加一个实现了 StateWrapper 接口的组件，直接将组件本身作为状态转换函数。
		return component(extraComponent, extraComponent);
	}

	public MultiblockRegistration<State> build(Consumer<Consumer<IEventBus>> registerToModBus)
	{
		Objects.requireNonNull(logic);
		Objects.requireNonNull(masterBE);
		Objects.requireNonNull(dummyBE);
		Objects.requireNonNull(block);
		Objects.requireNonNull(item);
		Objects.requireNonNull(getMasterPosInMB);
		Objects.requireNonNull(getSize);
		Objects.requireNonNull(disassemble);
		Objects.requireNonNull(structure);
		Preconditions.checkState(this.result==null);

		this.result = new MultiblockRegistration<>(
				logic, extraComponents, masterBE, dummyBE, block, item,
				mirrorable, hasComparatorOutput, redstoneInputAware, postProcessesShape,
				getMasterPosInMB, getSize, disassemble, structure, name
		);
		registerToModBus.accept(bus -> bus.addListener( // 注册能力
				RegisterCapabilitiesEvent.class, this::registerCapabilities
		));
		return this.result;
	}
```

好了到此位置，我们就返回了对应的<u>​`MultiblockRegistration`​</u>​，这个包含了多方快机器的一些数据。

‍
