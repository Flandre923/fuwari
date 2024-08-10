---
title: 沉浸工艺多方块06 方块实体部分
published: 2024-08-10
description: "沉浸工艺多方块"
image: "./assets/121261337_p0_master1200.jpg"
tags: ["我的世界", "沉浸工艺"]
category: 沉浸工艺
draft: false
---

# 06 方块实体的构建过程，tick过程

​`CokeOvenLogic`​对应的Logic类是多方块机器的行为逻辑地方，例如`CokeOvenLogic`​焦炉就需要处理焦炉的合成，以及煤焦油等产出，以及数据的保存和读取，等等。其实这个Logic的内容还是有具体的方块实体来调用的。

# 构建过程

方块实体的构建

​`MultiblockPartBlock`​

```java

	public BlockEntity newBlockEntity(@Nonnull BlockPos pos, @Nonnull BlockState state)
	{
		if(state.getValue(IEProperties.MULTIBLOCKSLAVE))
			return multiblock.dummyBE().get().create(pos, state); // 如果是一个从属的方块
		else
			return multiblock.masterBE().get().create(pos, state);
	}
```

方块实体会根据当前的方块是否是master方块决定是创建master方块实体还是,dummy方块实体,对于这两者的区别我们提到了很多次了就不重复了.

这里注意的是dummyBE()返回的是对应的构造方法,然后调用这个构造方法实现了创建对应的方块实体.

而构造方法是在你注册registration时候指定的,你可以回去看看上一个教程.

```java
	public MultiblockBlockEntityMaster(
			BlockEntityType<?> type,
			BlockPos worldPosition,
			BlockState blockState,
			MultiblockRegistration<State> multiblock
	)
	{
		super(type, worldPosition, blockState);
		this.helper = IMultiblockBEHelperMaster.MAKE_HELPER.get().makeFor(this, multiblock);
	}
```

会调用对应的方块实体的构造方法,对于是dummy则调用对应的dummy方块实体构造方法.在构造方法中会初始化helper,而方块实体的一些数据存储,tick等功能都会委托给helper进行调用.而这里的helper的创建方法则是之前IEContent初始化执行的. 对于dummyHelper的实例化也是类似的.

```java
// IEContent 初始化时候赋值的
IMultiblockBEHelperMaster.MAKE_HELPER.setValue(MultiblockBEHelperMaster::new);
```

调用构造方法进行初始化

```java

	public MultiblockBEHelperMaster(MultiblockBlockEntityMaster<State> be, MultiblockRegistration<State> multiblock)
	{
		super(be, multiblock, be.getBlockState());
		this.state = multiblock.logic().createInitialState(new InitialMultiblockContext<>(
				be, orientation, multiblock.masterPosInMB()
		)); // 其中这里是方块的实际的数据存储的位置
		// 多方快结构的原点位置
		final BlockPos multiblockOrigin = be.getBlockPos().subtract(
				orientation.getAbsoluteOffset(multiblock.masterPosInMB())
		);
		//
		final MultiblockLevel level = new MultiblockLevel(be::getLevel, this.orientation, multiblockOrigin);
		this.context = new MultiblockContext<>(this, multiblock, level);
		this.componentInstances = new ArrayList<>();
		for(ExtraComponent<State, ?> c : multiblock.extraComponents())
			this.componentInstances.add(ComponentInstance.make(c, this.state, this.context));
		//以根据多块大小和方向计算边界框。
		this.renderBox = new CachedValue<>((origin, orientation) -> {
			final BlockPos maxBlock = new BlockPos(multiblock.size(level.getRawLevel()));
			final Vec3 max = Vec3.atLowerCornerOf(maxBlock).add(1, 1, 1);
			final Vec3 absoluteOffset = orientation.getAbsoluteOffset(max);
			return new AABB(Vec3.ZERO, absoluteOffset).move(origin);
		});
	}
```

在这个构造方法中,其中的state的是存储的数据的位置.用焦炉作为例子,就是输入的物品,输出的物品,合成进度等等数据存储.这个state会调用logic下的createInitialState返回一个具体的state. 除此之外,helper还存储了原点的位置,机器有哪些组件.以及渲染时候显示的包围箱.

# tick方法

在注册对应的Registrarion中的注册的方块实体是`MultiblockPartBlock`​。

```java
// `MultiblockPartBlock`
	public <T extends BlockEntity> BlockEntityTicker<T> getTicker(
			@Nonnull Level level, @Nonnull BlockState state, @Nonnull BlockEntityType<T> actual
	)
	{
		if(state.getValue(IEProperties.MULTIBLOCKSLAVE)) // 从属方块不需要一个tick的回调
			return null;
		if(level.isClientSide&&needsClientTicker)
			return createTickerHelper(actual, ($1, $2, $3, blockEntity) -> blockEntity.getHelper().tickClient());
		if(!level.isClientSide&&needsServerTicker)
			return createTickerHelper(actual, ($1, $2, $3, blockEntity) -> blockEntity.getHelper().tickServer());
		return null;
	}

```

getTicker返回的是($1,$2, $3, blockEntity) -> blockEntity.getHelper().tickServer()这个lammbda表达式，这个就是tick的回调方法。而这个方法会通过对应的blockenetity获得对应的helper从而调用tickserver方法。只有Master的方块实体需要取处理对应的业务逻辑。所以这里得到的heleper是`MultiblockBEHelperMaster`​

​`MultiblockBEHelperMaster`​方法会负责处理能力，方块的tick方法，以及数据保存等内容

```java
//`MultiblockBEHelperMaster`
	public void tickServer()
	{
		if(!SafeChunkUtils.isChunkSafe(be.getLevel(), be.getBlockPos())) // 确保实体处于加载的区块
			return;
		final IMultiblockComponent<State> logic = multiblock.logic();
		if(logic instanceof IServerTickableComponent<State> serverTickable) // 调用tickserver的方法
			serverTickable.tickServer(getContext());
		for(final ComponentInstance<?> component : componentInstances)
			component.tickServer();// 组件如果实现了servertick的方法，就需要处理这个组件
	}
```

这个helper会根据当前机器logic的是否实现了IServerTickableComponent接口，如果实现就会调用到Logic的

```java
// CokeOvenLogic
	@Override
	public void tickServer(IMultiblockContext<State> context)
	{
```

最后会来到CokeOvenLogic，调用对应的Logic方法

‍

‍

# 方块的碰撞箱是怎么设置的

```java
// MultiblockPartBlock 机器方块，该方法返回对应的方块的碰撞箱。
	@Override
	public VoxelShape getCollisionShape(
			@Nonnull BlockState state,
			@Nonnull BlockGetter level,
			@Nonnull BlockPos pos,
			@Nonnull CollisionContext context
	)
	{
		return getShape(level, pos, context, ShapeType.COLLISION);
	}


/// 调用

	@Nonnull
	private VoxelShape getShape(
			@Nonnull BlockGetter level,
			@Nonnull BlockPos pos,
			@Nullable CollisionContext context,
			@Nonnull ShapeType type
	)
	{
		final BlockEntity bEntity = level.getBlockEntity(pos);
		if(bEntity instanceof IMultiblockBE<?> multiblockBE)
			return multiblockBE.getHelper().getShape(context, type);
		else
			return Shapes.block();
	}

```

这个getshape会获得对应的BEHelper方法获得对应的碰撞箱

```java

	@Override
	public VoxelShape getShape(@Nullable CollisionContext ctx, ShapeType type)
	{
		final BlockPos posInMB = getPositionInMB();//获取当前方块实体在多方块结构中的相对位置
		final IMultiblockLogic<State> logic = multiblock.logic();
		final VoxelShape absoluteShape = cachedShape.get(type).get(posInMB, orientation); // 
		if(ctx!=null&&multiblock.postProcessesShape())
		{
			final IMultiblockContext<State> multiblockCtx = getContext();
			if(multiblockCtx!=null)
				return logic.postProcessAbsoluteShape(multiblockCtx, absoluteShape, ctx, posInMB, type);
		}
		return absoluteShape;
	}
```

这个会根据posInMB在多方快结构的位置然后取logic中查询对应的位置下的碰撞箱应该是多大，这个不同的机器不一样，所以由具体的机器的logic实现。

```java
(pos, orientation) -> {
				final VoxelShape relative = multiblock.logic().shapeGetter(type).apply(pos);
				return orientation.transformRelativeShape(relative);
			}
```

会调用这个lammbda表达式，这个表达式就会到logic中查询对应的type的pos位置下的shape是什么样子。我们这里是

```java
// logic
	public Function<BlockPos, VoxelShape> shapeGetter(ShapeType forType)
	{
		return $ -> Shapes.block();
	}
```

即一整个方块都是碰撞箱，如果你是不同的大小可以通过这里的方法根据type,和pos来动态返回不同的大小shape

‍

# Save和Load

Master方块实体会将save和load委托给对于的helper,然后helper在根据state的方法和机器的`component`​进行nbt的写入和读取.

具体的流程就留给大家自己调试了.

‍

# 右键打开GUI流程

```java
// block
	@Nonnull
	@Override
	public InteractionResult use(
			@Nonnull BlockState state,
			@Nonnull Level level,
			@Nonnull BlockPos pos,
			@Nonnull Player player,
			@Nonnull InteractionHand hand,
			@Nonnull BlockHitResult hit
	)
	{
		final BlockEntity bEntity = level.getBlockEntity(pos);
		if(bEntity instanceof IMultiblockBE<?> multiblockBE)
			return multiblockBE.getHelper().click(player, hand, hit); //调用方块实体的对应的方法
		else
			return InteractionResult.PASS;
	}
```

首先对机器方块右键会调用这个方法,这个方法会获得对应的方块实体,然后获得方块实体的helper调用helper的click方法.在dummyHelper中.

```java
// dummy helper
	@Override
	public InteractionResult click(Player player, InteractionHand hand, BlockHitResult hit)
	{
		final MultiblockBEHelperMaster<State> helper = getMasterHelper();//调用 getMasterHelper 方法获取多方块结构的主辅助器对象 helper。
		if(helper==null)//如果 helper 为空，则表示没有找到主辅助器，直接返回失败 (InteractionResult.FAIL)。
			return InteractionResult.FAIL;
		final MultiblockContext<State> ctx = helper.getContext();//从主辅助器对象中获取多方块结构的上下文 (MultiblockContext<State>)，记为 ctx。
		for(final ComponentInstance<?> component : helper.getComponentInstances())
		{
			final InteractionResult componentResult = component.click(
					getPositionInMB(), player, hand, hit, player.level().isClientSide
			);
			if(componentResult!=InteractionResult.PASS)
				return componentResult;
		}
		//如果遍历所有组件都没有处理点击，则调用多方块结构的逻辑 (multiblock.logic()) 的 click 方法进行处理，将点击信息传递给多方块逻辑。
		return multiblock.logic().click(ctx, getPositionInMB(), player, hand, hit, player.level().isClientSide);
	}
```

在dummy的helper中,首先会获得master的Helper,然后获得机器的compoent逐渐,一次调用逐渐的click方法.对于GUI的组件,对应的click方法就是打开GUI.

```java

public record MultiblockGui<S extends IMultiblockState>(
		MultiblockContainer<S, ?> menu) implements IMultiblockComponent<S>
{
	@Override
	public InteractionResult click(
			IMultiblockContext<S> ctx,
			BlockPos posInMultiblock,
			Player player,
			InteractionHand hand,
			BlockHitResult absoluteHit,
			boolean isClient
	)
	{
		if(!isClient)
			player.openMenu(menu.provide(ctx, posInMultiblock)); // 打开GUI
		return InteractionResult.SUCCESS;
	}
}

```

这个激素hi一个Gui的组件.就会打开Gui了.

‍
