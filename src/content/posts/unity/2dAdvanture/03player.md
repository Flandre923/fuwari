---
title: Untity学习记录-实现一个完整的2D游戏Demo-03-设置人物基本组件
published: 2025-03-31
tags: ['Unity','游戏','demo','2d']
description: 使用Untiy时间一个2D冒险游戏demo
image: https://cdn.jsdelivr.net/gh/Flandre923/CDN@latest/img/20250331195820.png
category: Unity
draft: false
---
本博客是对于[勇者传说](https://www.bilibili.com/video/BV1uZ421U7Qz/?spm_id_from=333.1387.upload.video_card.click)教程的学习笔记。
**一、为 Player 添加物理组件：**

1. **Rigidbody 2D 组件：**

    * **作用：**  使物体具有物理属性，能够受到重力等物理规律的影响。
    * **添加方式：**  在 Inspector 窗口点击 "Add Component"，搜索并添加 "Rigidbody 2D"。
    * **默认属性：**  讲解了默认的重力比例为 1。
    * **项目设置 (Project Settings)：**  介绍了在 "Edit" -\> "Project Settings" -\> "Physics 2D" 中可以查看和修改全局物理设置，例如默认重力值（Y 轴 -9.81）和模拟模式 (Fixed Update)。
    * **Gravity Scale：**  说明此属性用于缩放全局重力值。
    * **效果：**  添加后，Player 会因为重力而下落。
2. **Collider 2D 组件 (Capsule Collider 2D)：**

    * **作用：**  定义物体的碰撞边界，用于检测与其他物体的接触。
    * **添加方式：**  在 Inspector 窗口点击 "Add Component"，搜索并添加 "Capsule Collider 2D"。
    * **编辑 Collider：**  使用 "Edit Collider" 按钮拖拽控制点来调整碰撞体的形状和大小，使其贴合 Player 的精灵图像。
    * **数值调整：**  可以直接在 Inspector 窗口中修改 Size 和 Offset 属性来精确调整碰撞体的位置和尺寸。

**二、为场景 (Tilemap) 添加碰撞组件：**

1. **Tilemap Collider 2D 组件：**

    * **作用：**  为 Tilemap 中的每个瓦片自动生成碰撞体。
    * **添加方式：**  在 Inspector 窗口选中 Tilemap 对象，点击 "Add Component"，搜索并添加 "Tilemap Collider 2D"。
    * **效果：**  每个被绘制的瓦片周围会出现绿色的碰撞体边框。
2. **Composite Collider 2D 组件：**

    * **作用：**  将多个独立的碰撞体合并为一个整体的碰撞体，提高性能。
    * **添加方式：**  在 Inspector 窗口选中 Tilemap 对象，点击 "Add Component"，搜索并添加 "Composite Collider 2D"。
    * **启用 Composite Collider：**  在 Tilemap Collider 2D 组件中勾选 "Used By Composite" 选项，触发合并。
    * **自动添加 Rigidbody 2D：**  添加 Composite Collider 2D 会自动为 Tilemap 添加 Rigidbody 2D 组件。
    * **设置 Body Type 为 Static：**  为了防止场景因重力而下落，需要将 Tilemap 的 Rigidbody 2D 组件的 "Body Type" 设置为 "Static"。

**三、防止 Player 旋转：**

1. **问题：**  Player 在碰撞或移动过程中可能会发生不希望的旋转。
2. **解决方案：**  在 Player 的 Rigidbody 2D 组件中，展开 "Constraints" 部分，勾选 "Freeze Rotation Z"，锁定 Player 在 Z 轴上的旋转。

**四、其他物理组件属性和优化：**

1. **质量 (Mass)：**  Rigidbody 2D 组件中的 "Mass" 属性决定了物体的质量，影响其受重力和碰撞时的反应。
2. **碰撞检测 (Collision Detection)：**

    * **Discrete (默认)：**  间歇性检测碰撞。
    * **Continuous：**  持续检测碰撞，适用于快速移动的物体，提高精度。
3. **插值 (Interpolate)：**

    * **None (默认)：**  不进行插值。
    * **Interpolate：**  基于前一帧的状态进行平滑插值。
    * **Extrapolate：**  基于历史运动预测当前位置进行外插。

**五、使用 Unity 文档 (Scripting API)：**

1. **访问方式：**  选中组件后，点击 Inspector 窗口右上角的问号图标。
2. **文档类型：**  提供在线和离线文档。
3. **语言切换：**  可以在文档页面切换语言，包括中文。
4. **作用：**  帮助开发者详细了解每个组件的属性、方法和工作原理。
5. **示例：**  演示了如何查找 Rigidbody 2D 组件的文档，并提到了中文文档中 "Sprite" 被翻译成 "雪碧" 的有趣现象。

**总结来说，讲解了如何为 Player 添加 Rigidbody 2D 和 Capsule Collider 2D 组件以实现基本的物理效果和碰撞检测，并为 Tilemap 添加 Tilemap Collider 2D 和 Composite Collider 2D 组件来创建场景的碰撞体。同时介绍了如何防止 Player 旋转，以及 Rigidbody 2D 组件中一些重要的属性和优化选项。最后强调了使用 Unity 官方文档进行学习的重要性。**