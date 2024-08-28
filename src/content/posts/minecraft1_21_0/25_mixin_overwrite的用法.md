---
title: 25 mixin overwrite的用法
published: 2024-08-24
tags: ['Minecraft1_21', 'Tutorial']
description: Neoforge Minecraft1.21 mafishmod 模组涉及的物品教程
image: https://cdn.jsdelivr.net/gh/Flandre923/CDN/img/6b33a98b-64eb-11ef-832d-b81ea485754c.jpg
category: Minecraft1_21_NeoForge_Tutorial
draft: false
---


# 25 mixin overwrite的用法

overwrite 可以直接重写原版的方法,不过这是极不推荐的, 因为这样的极有可能会和其他模组产生冲突. 这次的一个效果是希望有无限附魔的tnt能够一直爆炸.

```java
// 指定这是一个混入类，将功能添加到 TntBlock 类。
// 这允许我们修改 TntBlock 类的行为，而无需直接修改 TntBlock 的源代码。
@Mixin(TntBlock.class)
public abstract class TntBlockMixin extends Block {

    // 用于调用 TntBlock 类中受限的方法 explode。
    // 这个方法在 mixin 中被注解为 @Shadow，并且会被实际代码调用。
    @Shadow
    @Deprecated
    protected static void explode(Level level, BlockPos pos, @Nullable LivingEntity entity) {

    }

    // 构造函数，初始化 TntBlockMixin 类并调用父类 Block 的构造函数。
    public TntBlockMixin(Properties properties) {
        super(properties);
    }

    /**
     * @author
     * Mafuyu33
     * @reason
     * infinite explosion
     */
    @Overwrite
    // 重写 TntBlock 的 useItemOn 方法，处理 TNT 方块与物品交互的逻辑。
    protected ItemInteractionResult useItemOn(ItemStack stack, BlockState state, Level level, BlockPos pos, Player player, InteractionHand hand, BlockHitResult hitResult) {
        // 获取指定位置方块的附魔等级
        int k = BlockEnchantmentStorage.getLevel(Enchantments.INFINITY, pos);
        // 获取玩家手中的物品
        ItemStack itemStack = player.getItemInHand(hand);

        // 如果手中的物品不是打火石或火焰弹
        if (!itemStack.is(Items.FLINT_AND_STEEL) && !itemStack.is(Items.FIRE_CHARGE)) {
            // 调用父类的 useItemOn 方法
            return super.useItemOn(stack, state, level, pos, player, hand, hitResult);
        } else {
            // 调用 explode 方法，触发 TNT 爆炸
            explode(level, pos, player);

            // 如果附魔等级为 0，删除 TNT 方块
            if (k == 0) {
                level.setBlock(pos, Blocks.AIR.defaultBlockState(), 11); // 删除 TNT
            }

            // 获取手中的物品
            Item item = itemStack.getItem();
            // 如果玩家不是创造模式
            if (!player.isCreative()) {
                // 如果手中的物品是打火石，损耗耐久
                if (itemStack.is(Items.FLINT_AND_STEEL)) {
                    // itemStack.hurtAndBreak(1, player, (playerx) -> {
                    //     playerx.sendToolBreakStatus(hand);
                    // });
                    itemStack.hurtAndBreak(1, player, Objects.requireNonNull(itemStack.getEquipmentSlot()));
                } else {
                    // 如果是其他物品，减少数量
                    itemStack.shrink(1);
                }
            }

            // 统计玩家使用了物品
            player.awardStat(Stats.ITEM_USED.get(item));
            // 返回与物品交互的结果
            return ItemInteractionResult.sidedSuccess(level.isClientSide);
        }
    }

    /**
     * @author
     * Mafuyu33
     * @reason
     * infinite explosion
     */
    @Overwrite
    // 重写 TntBlock 的 wasExploded 方法，处理 TNT 方块被爆炸的逻辑。
    public void wasExploded(Level world, BlockPos pos, Explosion explosion) {
        // 如果不是客户端
        if (!world.isClientSide) {
            // 获取指定位置方块的附魔等级
            int k = BlockEnchantmentStorage.getLevel(Enchantments.INFINITY, pos);
            // 如果附魔等级大于 0
            if (k > 0) {
                // 获取附魔信息列表
                ListTag enchantments = BlockEnchantmentStorage.getEnchantmentsAtPosition(pos);
                // 存储附魔信息
                BlockEnchantmentStorage.addBlockEnchantment(pos, enchantments);
                // 重新设置方块为 TNT
                world.setBlock(pos, Blocks.TNT.defaultBlockState(), 16); // 添加 TNT
            }

            // 创建并添加一个新的 PrimedTnt 实体
            PrimedTnt tntEntity = new PrimedTnt(world, (double) pos.getX() + 0.5, (double) pos.getY(), (double) pos.getZ() + 0.5, explosion.getIndirectSourceEntity());
            // 设置 TNT 的引信时间
            int i = tntEntity.getFuse();
            tntEntity.setFuse((short) (world.random.nextInt(i / 4) + i / 8));
            // 将 TNT 实体添加到世界中
            world.addFreshEntity(tntEntity);
        }
    }

    /**
     * @author
     * Mafuyu33
     * @reason
     * infinite explosion
     */
    @Overwrite
    // 重写 TntBlock 的 onProjectileHit 方法，处理射弹击中 TNT 方块的逻辑。
    public void onProjectileHit(Level world, BlockState state, BlockHitResult hit, Projectile projectile) {
        // 如果不是客户端
        if (!world.isClientSide) {
            // 获取射弹击中的方块位置
            BlockPos blockPos = hit.getBlockPos();
            // 获取指定位置方块的附魔等级
            int k = BlockEnchantmentStorage.getLevel(Enchantments.INFINITY, blockPos);
            // 获取射弹的拥有者
            Entity entity = projectile.getOwner();
            // 如果射弹着火且可以与方块交互
            if (projectile.isOnFire() && projectile.mayInteract(world, blockPos)) {
                // 触发 TNT 爆炸
                explode(world, blockPos, entity instanceof LivingEntity ? (LivingEntity) entity : null);
                // 如果附魔等级为 0，移除 TNT 方块
                if (k == 0) {
                    world.removeBlock(blockPos, false);
                }
            }
        }
    }
}

```