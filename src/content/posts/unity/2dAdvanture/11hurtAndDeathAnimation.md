---
title: Untity学习记录-实现一个完整的2D游戏Demo-11-受伤和死亡的逻辑动画
published: 2025-03-31
tags: ['Unity','游戏','demo','2d']
description: 使用Untiy时间一个2D冒险游戏demo
image: https://cdn.jsdelivr.net/gh/Flandre923/CDN@latest/img/20250331195820.png
category: Unity
draft: false
---
**一、角色受伤和死亡动画的准备：**

1. **查找受伤和死亡动画帧：**  在 Art Assets 的玩家素材中，查看 Guide 图片（盖一），找到受伤动画 (Take Damage，编号 38-41) 和死亡动画 (Death Animation，编号 42-53)。死亡动画占据了两行。
2. **创建受伤动画 (blue hurt)：**  创建一个新的 Animation Clip，命名为 "blue hurt"，并将编号 38-41 的动画帧拖拽到时间轴上，调整采样率。
3. **创建死亡动画 (blue dead)：**  创建一个新的 Animation Clip，命名为 "blue dead"，并将编号 42-53 的动画帧拖拽到时间轴上，调整采样率（可适当减慢）。

**二、实现受伤动画的两种方式：**

1. **使用精灵帧动画 (blue hurt)：**  直接使用创建的 "blue hurt" 动画片段。
2. **使用 Animator 的 Additive Layer 实现闪烁效果 (hurt layer)：**

    * 在 Animator 窗口左上角的 Layer 菜单中创建一个新的 Layer，命名为 "hurt layer"。
    * 将 "hurt layer" 的 Blending Mode 设置为 "Additive"（叠加）。
    * 在 "hurt layer" 中创建一个新的空的动画片段。
    * 创建一个新的动画片段 "player ht 2"，不添加任何精灵帧。
    * 通过录制动画的方式，修改 Player 的 SpriteRenderer 组件的 Material Color 的 Alpha 值，使其在动画播放过程中闪烁，实现受伤效果。

**三、触发受伤动画：**

1. **创建 Hurt Trigger 参数：**  在 Animator 的 Base Layer 中创建一个新的 Trigger 类型的参数，命名为 "hurt"。
2. **创建受伤动画的 Transition：**  从 "Any State" 创建一个 Transition 到 "blue hurt" 动画状态，触发条件为 "hurt" 被触发。取消勾选 "Has Exit Time" 和 "Transition Duration"。
3. **创建返回 Transition：**  从 "blue hurt" 动画状态创建一个 Transition 返回到之前的动画状态。

**四、代码中触发受伤动画：**

1. **创建 PlayHurt 函数：**  在 `PlayerAnimation` 脚本中创建一个名为 `PlayHurt()` 的函数。
2. **设置 Hurt Trigger：**  在 `PlayHurt()` 函数中使用 `anim.SetTrigger("hurt");` 来触发 Animator 中的 "hurt" Trigger。

**五、使用 Unity Event 处理受伤逻辑：**

1. **导入 UnityEvent 命名空间：**  在 `Character` 脚本中导入 `UnityEngine.Events` 命名空间。
2. **创建 onTakeDamage 事件：**  在 `Character` 脚本中创建一个 `public UnityEvent<Transform> onTakeDamage` 事件。使用 `Transform` 类型作为泛型参数，用于传递攻击者的 Transform 信息，方便实现击退效果。
3. **触发 onTakeDamage 事件：**  在 `Character` 脚本的 `TakeDamage()` 函数中，判断 `onTakeDamage` 是否有监听者，如果有则使用 `onTakeDamage?.Invoke(attacker.transform);` 触发事件，并将攻击者的 Transform 传递出去。

**六、将受伤动画连接到 Unity Event：**

1. **在 Inspector 中添加事件监听：**  选中 Player 对象，在 Inspector 窗口中找到 "Character (Script)" 组件的 "On Take Damage (Transform)" 事件。
2. **添加 PlayHurt 函数到事件：**  点击 "+" 按钮添加一个新的事件监听，将 Player 对象拖拽到对象字段，然后在函数下拉菜单中选择 `PlayerAnimation` 脚本的 `PlayHurt()` 函数（选择 Runtime Only）。

**七、实现受伤击退效果：**

1. **创建 GetHurt 函数：**  在 `PlayerController` 脚本中创建一个名为 `GetHurt(Transform attackerTransform)` 的函数，接收攻击者的 Transform 作为参数。
2. **创建 isHurt 和 hurtForce 变量：**  在 `PlayerController` 脚本中声明 `public bool isHurt` 和 `public float hurtForce` 变量。
3. **实现击退逻辑：**  在 `GetHurt()` 函数中，将 `isHurt` 设置为 true，将 Rigidbody 2D 的速度设置为零，计算击退方向（玩家 X 坐标减去攻击者 X 坐标，并进行归一化），然后使用 `rb.AddForce(direction * hurtForce, ForceMode2D.Impulse)` 添加一个瞬时力。
4. **阻止受伤时的移动：**  在 `PlayerController` 脚本的 `Move()` 函数中添加判断：`if (!isHurt)`，只有当 `isHurt` 为 false 时才允许移动。

**八、将击退效果连接到 Unity Event：**

1. **在 Inspector 中添加事件监听：**  在 Player 对象的 "On Take Damage (Transform)" 事件中再次点击 "+" 按钮添加一个新的事件监听。
2. **添加 GetHurt 函数到事件：**  将 Player 对象拖拽到对象字段，然后在函数下拉菜单中选择 `PlayerController` 脚本的 `GetHurt(Transform)` 函数（选择 Dynamic Transform）。

**九、动画结束后重置 IsHurt 状态：**

1. **创建 HurtAnimation 脚本：**  创建一个新的 C# 脚本，命名为 "HurtAnimation"，继承自 `StateMachineBehaviour`。
2. **添加到 Hurt 动画状态：**  将 "HurtAnimation" 脚本附加到 Animator 中 "blue hurt" 动画状态上。
3. **重写 OnStateExit 函数：**  在 "HurtAnimation" 脚本中重写 `OnStateExit()` 函数。
4. **重置 IsHurt 状态：**  在 `OnStateExit()` 函数中，获取 Animator 所在游戏对象的 `PlayerController` 组件，并将 `isHurt` 设置为 `false`。

**十、实现死亡动画：**

1. **切换 Hurt Layer 的 Blending Mode：**  将 "hurt layer" 的 Blending Mode 切换回 "Override"。
2. **添加死亡动画到 Hurt Layer：**  将 "blue dead" 动画片段拖拽到 "hurt layer" 中。
3. **创建 IsDead 布尔参数：**  在 Animator 中创建一个新的 Bool 类型的参数，命名为 "isDead"。
4. **创建死亡动画的 Transition：**  从 "Any State" 创建一个 Transition 到 "blue dead" 动画状态，触发条件为 "isDead is true"。取消勾选 "Has Exit Time" 和 "Transition Duration"，并取消勾选 "Can Transition To Self"。
5. **创建返回 Transition (重新开始游戏)：**  从 "blue dead" 动画状态创建一个 Transition 返回到 "Any State"，触发条件为 "isDead is false"。
6. **设置死亡动画为单次播放：**  在 Project 窗口中选中 "blue dead" 动画片段，取消勾选 Inspector 窗口中的 "Loop Time"。

**十一、使用 Unity Event 处理死亡逻辑：**

1. **创建 onDie 事件：**  在 `Character` 脚本中创建一个 `public UnityEvent onDie` 事件。
2. **触发 onDie 事件：**  在 `Character` 脚本的 `TakeDamage()` 函数中，当 `currentHealth` 减到零时，使用 `onDie?.Invoke();` 触发 `onDie` 事件。

**十二、实现玩家死亡逻辑：**

1. **创建 PlayerDead 函数：**  在 `PlayerController` 脚本中创建一个名为 `PlayerDead()` 的函数。
2. **设置 IsDead 状态和禁用输入：**  在 `PlayerDead()` 函数中，将 `public bool isDead` 设置为 true，并使用 `input.Gameplay.Disable();` 禁用 "Gameplay" Input Action Map，停止玩家操作。

**十三、将死亡动画和逻辑连接到 Unity Event：**

1. **在 Inspector 中添加事件监听：**  选中 Player 对象，在 Inspector 窗口中找到 "Character (Script)" 组件的 "On Die" 事件。
2. **设置 IsDead 参数：**  点击 "+" 按钮添加一个新的事件监听，将 Player 对象的 Animator 组件拖拽到对象字段，然后在函数下拉菜单中选择 "isDead" 参数并勾选复选框以将其设置为 true。
3. **添加 PlayerDead 函数到事件：**  再次点击 "+" 按钮添加一个新的事件监听，将 Player 对象拖拽到对象字段，然后在函数下拉菜单中选择 `PlayerController` 脚本的 `PlayerDead()` 函数（选择 Runtime Only）。

总而言之，讲解了如何在 Unity 中实现玩家受到伤害时的动画反馈（包括使用叠加图层实现闪烁效果和使用精灵帧动画），以及当玩家生命值降为零时播放死亡动画。同时，还介绍了如何利用 Unity Event 系统来解耦代码，方便地触发多个不同的行为（如播放动画、执行脚本逻辑等），并学习了如何在角色死亡后禁用玩家的输入操作