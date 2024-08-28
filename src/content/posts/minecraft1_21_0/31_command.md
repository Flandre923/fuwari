---
title: 31 command
published: 2024-08-24
tags: ['Minecraft1_21', 'Tutorial']
description: Neoforge Minecraft1.21 mafishmod 模组涉及的物品教程
image: https://cdn.jsdelivr.net/gh/Flandre923/CDN/img/737b4705-64eb-11ef-a533-b81ea485754c.jpg
category: Minecraft1_21_NeoForge_Tutorial
draft: false
---


# 31 command

这次我们看看怎么执行指令

做一个弓箭可以通过执行tick指令调整游戏tick刻，实现时间流逝速度变慢效果，并添加一个药水的夜市效果模拟这种视觉，

```java
package com.mafuyu33.neomafishmod.mixin.itemmixin;

@Mixin(Player.class)
public abstract class BowDashMixin extends LivingEntity {
    // 构造函数，继承自LivingEntity
    protected BowDashMixin(EntityType<? extends LivingEntity> entityType, Level level) {
        super(entityType, level);
    }

    // Shadow方法，允许访问Player类中的私有方法
    @Shadow
    public abstract void tick();

    // Unique字段，用于记录冲刺冷却时间
    @Unique
    int BowDashCoolDown = 0;

    // 在Player类的tick方法中注入代码
    @Inject(at = @At("HEAD"), method = "tick")
    private void init1(CallbackInfo ci) {
        boolean isBowDashable = Config.isBowDashable(); // 从配置文件读取是否启用弓冲刺功能
        if (isBowDashable) {
            // 如果启用弓冲刺功能
            if (level().isClientSide && this.isHolding(Items.BOW) && this.isUsingItem()
                    && BowDashMixinHelper.isAttackKeyPressed() && BowDashCoolDown <= 0) { // 如果在客户端并且玩家拿着弓并使用它，并且攻击键被按下，且冷却时间为0
                // 获取玩家的速度矢量
                Vec3 velocity = this.getDeltaMovement();
                // 投影到水平平面上
                Vec3 horizontalMotion = new Vec3(velocity.x, 0, velocity.z);
                // 将水平移动向量标准化
                if (horizontalMotion.lengthSqr() > 0) {
                    horizontalMotion = horizontalMotion.normalize();
                }
                float amp = 2; // 冲刺的强度
                this.push(amp * horizontalMotion.x, 0.14, amp * horizontalMotion.z); // 推动玩家向前移动

                sendC2S(); // 发送客户端到服务器的数据包

                System.out.println("突进"); // 控制台输出信息

                level().playSound(this, this.blockPosition(), ModSounds.DASH_SOUND.value(), SoundSource.PLAYERS, 1f, 1f); // 播放冲刺音效
                BowDashCoolDown = 20; // 设置冷却时间为20个游戏刻
            }

            if (level().isClientSide && BowDashCoolDown > 0) { // 如果在客户端并且冲刺冷却时间大于0
                BowDashCoolDown--; // 减少冷却时间
//            System.out.println(BowDashCoolDown); // 输出剩余冷却时间到控制台
                sendC2S(); // 发送客户端到服务器的数据包
            }


            if (!level().isClientSide && BowDashMixinHelper.getHitCoolDown(this.getId()) != 0) {//时间变慢部分
                MinecraftServer server = this.getServer();
                if (server != null) {
                    if (BowDashMixinHelper.getHitCoolDown(this.getId()) >= 12) {
                        // 获取服务器命令调度程序
                        CommandDispatcher<CommandSourceStack> dispatcher = server.getCommands().getDispatcher();
                        try {
                            // 解析指令并获取命令源
                            ParseResults<CommandSourceStack> parseResults
                                    = dispatcher.parse("gamerule sendCommandFeedback false", server.createCommandSourceStack());
                            // 执行指令
                            dispatcher.execute(parseResults);

                            // 在控制台输出提示信息
                        } catch (CommandSyntaxException e) {
                            // 指令语法异常处理
                            e.printStackTrace();
                        }
                        try {
                            // 解析指令并获取命令源
                            ParseResults<CommandSourceStack> parseResults
                                    = dispatcher.parse("tick rate 6", server.createCommandSourceStack());
                            // 执行指令
                            dispatcher.execute(parseResults);

                            // 在控制台输出提示信息
                        } catch (CommandSyntaxException e) {
                            // 指令语法异常处理
                            e.printStackTrace();
                        }
                        try {
                            // 解析指令并获取命令源
                            ParseResults<CommandSourceStack> parseResults
                                    = dispatcher.parse("effect give @p minecraft:slowness infinite 3 true", server.createCommandSourceStack());
                            // 执行指令
                            dispatcher.execute(parseResults);

                            // 在控制台输出提示信息
                        } catch (CommandSyntaxException e) {
                            // 指令语法异常处理
                            e.printStackTrace();
                        }
                        try {
                            // 解析指令并获取命令源
                            ParseResults<CommandSourceStack> parseResults
                                    = dispatcher.parse("effect give @p minecraft:night_vision infinite 0 true", server.createCommandSourceStack());
                            // 执行指令
                            dispatcher.execute(parseResults);

                            // 在控制台输出提示信息
                        } catch (CommandSyntaxException e) {
                            // 指令语法异常处理
                            e.printStackTrace();
                        }
                    } else {
                        // 获取服务器命令调度程序
                        CommandDispatcher<CommandSourceStack> dispatcher = server.getCommands().getDispatcher();
                        try {
                            // 解析指令并获取命令源
                            ParseResults<CommandSourceStack> parseResults
                                    = dispatcher.parse("tick rate 20", server.createCommandSourceStack());
                            // 执行指令
                            dispatcher.execute(parseResults);

                            // 在控制台输出提示信息
                        } catch (CommandSyntaxException e) {
                            // 指令语法异常处理
                            e.printStackTrace();
                        }
                        try {
                            // 解析指令并获取命令源
                            ParseResults<CommandSourceStack> parseResults
                                    = dispatcher.parse("effect clear @p minecraft:slowness", server.createCommandSourceStack());
                            // 执行指令
                            dispatcher.execute(parseResults);

                            // 在控制台输出提示信息
                        } catch (CommandSyntaxException e) {
                            // 指令语法异常处理
                            e.printStackTrace();
                        }
                        try {
                            // 解析指令并获取命令源
                            ParseResults<CommandSourceStack> parseResults
                                    = dispatcher.parse("effect clear @p minecraft:night_vision", server.createCommandSourceStack());
                            // 执行指令
                            dispatcher.execute(parseResults);

                            // 在控制台输出提示信息
                        } catch (CommandSyntaxException e) {
                            // 指令语法异常处理
                            e.printStackTrace();
                        }
                    }
                }
            }
        }
    }
    // 客户端发送数据包到服务端的方法
    @Unique
    @OnlyIn(Dist.CLIENT)
    private void sendC2S() {
//        PacketByteBuf buf = PacketByteBufs.create();//传输到服务端
//        buf.writeInt(BowDashCoolDown);
//        ClientPlayNetworking.send(ModMessages.Bow_Dash_ID, buf);
        PacketDistributor.sendToServer(new BowDashC2SPacket(BowDashCoolDown));
    }
}
```

‍