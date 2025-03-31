---
title: Untity学习记录-实现一个完整的2D游戏Demo-09-创建人物跳跃动画
published: 2025-03-31
tags: ['Unity','游戏','demo','2d']
description: 使用Untiy时间一个2D冒险游戏demo
image: https://cdn.jsdelivr.net/gh/Flandre923/CDN@latest/img/20250331195820.png
category: Unity
draft: false
---
讲解了如何使用 Unity 的 Blend Tree 功能来制作人物跳跃的动画，相比于创建多个独立的动画状态和复杂的切换条件，Blend Tree 可以根据一个参数的值在多个动画之间进行平滑的混合。

**一、跳跃动画阶段分析：**

1. **五阶段跳跃：**  将跳跃动画分解为五个阶段：准备起跳、起飞、空中上升、空中下降、落地。
2. **素材命名：**  建议对跳跃动画的帧进行重命名，方便在 Unity 中查找和管理 (例如 jump\_1-1, jump\_1-2, jump\_2-1 等)。

**二、创建跳跃动画片段：**

1. **为每个阶段创建 Animation Clip：**  针对跳跃的五个阶段，分别创建对应的 Animation Clip (Jump\_1, Jump\_2, Jump\_3, Jump\_4, Land)，并将对应的动画帧拖拽到时间轴上，设置 Sample Rate 为 13。

**三、使用 Blend Tree 混合动画：**

1. **删除之前的跳跃状态：**  将之前创建的单个跳跃动画状态从 Animator 窗口中删除。
2. **创建新的 Blend Tree：**  在 Animator 窗口空白处右键点击，选择 "Create State" -\> "From New Blend Tree"，并将新的 Blend Tree 命名为 "Jump"。
3. **进入 Blend Tree：**  双击 "Jump" Blend Tree 进入其编辑界面。
4. **设置 Blend Type 为 1D：**  在 Blend Tree 的 Inspector 窗口中，将 "Blend Type" 设置为 "1D"，因为跳跃动画主要根据垂直速度变化。
5. **创建 velocityY 参数：**  在 Animator 窗口的 "Parameters" 选项卡中创建一个新的 "Float" 参数，命名为 "velocityY"，用于控制 Blend Tree 的混合。
6. **选择 Parameter：**  在 Blend Tree 的 Inspector 窗口中，将 "Parameter" 设置为刚刚创建的 "velocityY"。
7. **添加 Motion：**  在 Blend Tree 的 "Motions" 列表中逐个添加之前创建的五个跳跃动画片段 (Jump\_1, Jump\_2, Jump\_3, Jump\_4, Land)。
8. **取消 Automate Thresholds：**  取消勾选 Blend Tree Inspector 中的 "Automate Thresholds"，以便手动设置动画切换的阈值。

**四、确定 Threshold 值：**

1. **观察垂直速度：**  运行游戏，在 Player 的 Rigidbody 2D 组件的 Info 面板中观察跳跃过程中 Y 轴速度 (velocityY) 的变化范围 (正值上升，负值下降)。
2. **设置 Threshold 值：**  根据观察到的速度范围，为 Blend Tree 中的每个动画片段设置合适的 Threshold 值，决定在哪个垂直速度值时播放哪个动画：

    * Jump\_1 (起跳准备)：较高的正值 (例如 13)。
    * Jump\_2 (起飞)：稍低的正值 (例如 8 或 9)。
    * Jump\_3 (空中最高点)：接近 0 的值 (例如 3 或 4.5)。
    * Jump\_4 (下降)：负值 (例如 -3 或 -4)。
    * Land (落地)：较大的负值 (例如 -13 或 -14)。

**五、代码同步 velocityY 参数：**

1. **修改 PlayerAnimation 脚本：**  在 `PlayerAnimation` 脚本的 `SetAnimation()` 函数中，添加 `anim.SetFloat("velocityY", rb.velocity.y);`，将 Rigidbody 2D 的垂直速度实时同步到 Animator 的 "velocityY" 参数。

**六、创建跳跃动画的切换逻辑 (Base Layer)：**

1. **创建 isGround 布尔参数：**  在 Animator 窗口的 "Parameters" 选项卡中创建一个新的 "Bool" 参数，命名为 "isGround"，用于判断角色是否在地面上。
2. **Any State 到 Jump 的 Transition：**  从 "Any State" 右键拖拽到 "Jump" Blend Tree 创建一个 Transition。
3. **设置 Transition 条件：**  选中该 Transition，设置 Condition 为 "isGround is false"，表示不在地面时进入跳跃状态。取消勾选 "Has Exit Time"，设置 "Transition Duration" 为 0，并取消勾选 "Can Transition To Self"。
4. **Jump 到 Land 的 Transition：**  从 "Jump" Blend Tree 右键拖拽到 "Land" 动画片段创建一个 Transition。
5. **设置 Transition 条件：**  选中该 Transition，设置 Condition 为 "isGround is true"，表示回到地面时进入落地状态。
6. **Land 到 Idle 的 Transition：**  从 "Land" 动画片段右键拖拽到 "Idle" 状态创建一个 Transition，勾选 "Has Exit Time" 并设置为 1，表示落地动画播放完毕后切换到 Idle。
7. **Land 到 Run 的 Transition (打断落地动画)：**  从 "Land" 动画片段右键拖拽到 "Run" 状态创建一个 Transition，取消勾选 "Has Exit Time"，设置 Condition 为 "velocityX Greater than 0.1"，表示落地后立即开始跑动时打断落地动画。

**七、代码同步 isGround 参数：**

1. **获取 PhysicsCheck 组件：**  在 `PlayerAnimation` 脚本中获取 `PhysicsCheck` 组件的引用。
2. **同步 isGround 参数：**  在 `SetAnimation()` 函数中添加 `anim.SetBool("isGround", physicsCheck.isGround);`，将 `PhysicsCheck` 脚本中的 `isGround` 变量的值同步到 Animator 的 "isGround" 参数。

**总结来说，讲解了如何使用 Blend Tree 功能，根据角色的垂直速度平滑地切换跳跃动画的不同阶段，并通过代码将 Rigidbody 2D 的垂直速度和接地状态同步到 Animator 中，实现了更自然和流畅的跳跃动画效果。同时还介绍了如何使用 &quot;Any State&quot; 进行状态打断，以及如何处理落地后立即跑步的动画切换。**