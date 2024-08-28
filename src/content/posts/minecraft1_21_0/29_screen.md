---
title: 29 screen
published: 2024-08-24
tags: ['Minecraft1_21', 'Tutorial']
description: Neoforge Minecraft1.21 mafishmod 模组涉及的物品教程
image: https://cdn.jsdelivr.net/gh/Flandre923/CDN/img/70f2906d-64eb-11ef-a3a2-b81ea485754c.jpg
category: Minecraft1_21_NeoForge_Tutorial
draft: false
---


# 29 screen

screen是客户端绘制界面的，例如你的背包界面，以及熔炉界面，这个是仅存在客户端的东西。

这次通过原版的背包的screen，实现一个拿玩家拿不到物品的机制

```java
package com.mafuyu33.neomafishmod.mixin.enchantmentitemmixin.slippery;
@Mixin(InventoryScreen.class)
// 定义一个 Mixin 类，混入到 `InventoryScreen` 类中。
public abstract class InventoryScreenMixin extends EffectRenderingInventoryScreen<InventoryMenu> {

    public InventoryScreenMixin(InventoryMenu menu, Inventory playerInventory, Component title) {
        super(menu, playerInventory, title);
    }

    @Inject(at = @At(value = "HEAD"), method = "mouseClicked", cancellable = true)
    private void init(double mouseX, double mouseY, int button, CallbackInfoReturnable<Boolean> cir) {
        Slot slot = this.findSlot(mouseX, mouseY); // 查找鼠标点击位置的槽位。
        System.out.println(slot); // 打印槽位的信息。
        if (slot != null) {
            ItemStack itemStack = slot.getItem(); // 获取槽位中的物品。
            System.out.println(itemStack); // 打印物品的信息。
            System.out.println(itemStack.getEnchantments()); // 打印物品上的所有附魔。
            if (InjectHelper.getEnchantmentLevel(itemStack, ModEnchantments.SLIPPERY) > 0) {
                // 如果物品有 SLIPPERY 附魔。
                if (placeItemInPlayerInventory(this.minecraft.player, itemStack)) {
                    // 将物品放入玩家的背包中。
                    cir.cancel(); // 取消默认的鼠标点击行为。
                }
            }
        }
    }

    // 在玩家背包的随机位置放置物品
    @Unique
    private static boolean placeItemInPlayerInventory(Player player, ItemStack itemStack) {
        Inventory playerInventory = player.getInventory(); // 获取玩家的库存。
        int count = itemStack.getCount(); // 获取物品的数量。
        NonNullList<ItemStack> slots = playerInventory.items; // 获取玩家库存中的所有槽位。
        Random random = new Random(); // 创建随机数生成器。
        int attempts = 0; // 初始化尝试次数。
        while (attempts < 100) { // 尝试最多100次。
            int slotIndex = random.nextInt(slots.size()); // 生成随机槽位索引。
            ItemStack slotStack = slots.get(slotIndex); // 获取槽位中的物品。
            if (slotStack.isEmpty()) { // 如果槽位为空。
                slots.set(slotIndex, itemStack.copy()); // 将物品放入槽位。
                itemStack.shrink(count); // 减少物品的数量。
                System.out.println("已将物品放置到背包中"); // 输出提示信息。
                return true; // 返回成功。
            }
            attempts++; // 尝试下一个槽位。
        }
        System.out.println("无法找到空槽位放置物品"); // 如果没有找到空槽位，则输出提示信息。
        return false; // 返回失败。
    }
}
```

‍