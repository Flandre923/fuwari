---
title: Untity学习记录-实现一个完整的2D游戏Demo-08-创建人物基本动画
published: 2025-03-31
tags: ['Unity','游戏','demo','2d']
description: 使用Untiy时间一个2D冒险游戏demo
image: https://cdn.jsdelivr.net/gh/Flandre923/CDN@latest/img/20250331195820.png
category: Unity
draft: false
---
总结其中的知识点：讲解如何在 Unity 中制作人物的动画，使用的是帧动画的技术，并介绍了 Unity 的动画系统。

**一、动画素材准备：**

1. **查看素材指南：**  强调查看提供的素材指南图片（盖一和盖二），这些图片标注了哪些帧用于制作不同的动画状态 (Idle, Run, Jump, Crouch, Hit, Death, Walk)。
2. **素材命名：**  素材图片的命名规则 (例如 character\_blue\_1\_0, character\_blue\_1\_1) 可以帮助识别不同的动画帧。

**二、添加 Animator 组件：**

1. **选择 Player 对象：**  选中需要添加动画的 Player 游戏对象。
2. **添加 Animator 组件：**  在 Inspector 窗口点击 "Add Component"，搜索并添加 "Animator" 组件。
3. **Animator 控制器：**  解释了 Animator 组件需要一个 Animator Controller 来管理动画状态。

**三、创建 Animator Controller：**

1. **创建 Animations 文件夹：**  在 Project 窗口创建一个名为 "Animations" 的文件夹，用于存放动画资源。
2. **创建 Player 子文件夹：**  在 "Animations" 文件夹下创建一个名为 "Player" 的子文件夹，用于存放 Player 的动画资源。
3. **创建 Animator Controller 资源：**  在 "Animations/Player" 文件夹中点击 "+" 按钮，选择 "Animator Controller"，并命名为 "Player"。
4. **将 Animator Controller 赋给 Animator 组件：**  选中 Player 对象，将创建好的 "Player" Animator Controller 拖拽到 Inspector 窗口中 Animator 组件的 "Controller" 属性上。

**四、打开 Animator 窗口：**

1. **Window -&gt;**  **Animation -&gt;**  **Animator：**  打开 Animator 窗口，用于编辑动画状态和切换逻辑。
2. **Animator 窗口操作：**  介绍了 Animator 窗口的基本操作，如使用鼠标中键拖拽和鼠标右键创建空状态。

**五、创建 Idle 动画：**

1. **打开 Animation 窗口 (Timeline)：**  选中 Player 对象，打开 "Window" -\> "Animation" -\> "Animation" 窗口。
2. **创建新的 Animation Clip：**  在 Animation 窗口点击 "Create" 按钮，将新的 Animation Clip 保存到 "Animations/Player" 文件夹，命名为 "blue\_idle"。
3. **默认动画状态：**  创建的动画会自动添加到 Animator 窗口，并被标记为黄色，表示默认播放状态。
4. **添加动画帧：**  按住 Shift 键选择 Idle 动画对应的图片帧 (0-5)，拖拽到 Animation 窗口的时间轴上。
5. **调整 Sample Rate：**  在 Animation 窗口右侧的三个点菜单中勾选 "Show Sample Rate"，然后在时间轴上方设置 Sample Rate (帧率) 为 10，控制动画播放速度。
6. **预览动画：**  点击 Animation 窗口的播放按钮预览动画，预览结束后再次点击 "Preview" 按钮还原状态。

**六、创建 Run 动画：**

1. **创建新的 Animation Clip：**  在 Animation 窗口左侧的下拉菜单中选择 "Create New Clip"，命名为 "blue\_run"，保存到 "Animations/Player" 文件夹。
2. **添加动画帧：**  选择 Run 动画对应的图片帧 (14-21)，拖拽到 Animation 窗口的时间轴上。
3. **设置 Sample Rate：**  将 Run 动画的 Sample Rate 设置为 14。
4. **Animator 状态重命名：**  在 Animator 窗口中，可以看到新创建的 "blue\_run" 状态，可以将其重命名为 "Run"。

**七、创建动画状态之间的切换 (Transition)：**

1. **创建 Transition：**  在 Animator 窗口中，右键点击 "Idle" 状态，选择 "Make Transition"，然后点击 "Run" 状态，创建从 Idle 到 Run 的切换箭头。
2. **Transition 设置：**  选中 Transition 箭头，在 Inspector 窗口中可以进行各种设置，包括 "Has Exit Time" (动画播放完成一定比例后切换)，"Fixed Duration" (固定切换时间) 和 "Transition Duration" (切换过渡时间)。
3. **创建 Parameters：**  在 Animator 窗口左上角的 "Parameters" 选项卡中，点击 "+" 按钮创建动画切换的条件变量。这里创建了一个 "Float" 类型的变量，命名为 "velocityX"。
4. **添加 Transition 条件：**  选中从 "Idle" 到 "Run" 的 Transition，取消勾选 "Has Exit Time" 和 "Fixed Duration"，添加一个 Condition：当 "velocityX" 大于 0.1 时切换到 Run 状态。
5. **创建反向 Transition：**  创建从 "Run" 到 "Idle" 的 Transition，添加 Condition：当 "velocityX" 小于 0.1 时切换回 Idle 状态。

**八、创建 PlayerAnimation 脚本控制 Animator：**

1. **创建 PlayerAnimation 脚本：**  在 "Player" 文件夹下创建一个新的 C# 脚本，命名为 "PlayerAnimation"。
2. **添加到 Player 对象：**  将 "PlayerAnimation" 脚本添加到 Player 对象上，并调整其在 Inspector 窗口中的顺序，将 Animator 组件放在顶部。
3. **获取 Animator 组件：**  在 `PlayerAnimation` 脚本的 `Awake()` 函数中，使用 `anim = GetComponent<Animator>();` 获取 Animator 组件的引用。
4. **创建 SetAnimation 函数：**  创建一个 `void SetAnimation()` 函数来处理动画状态的设置。
5. **在 Update 中调用 SetAnimation：**  在 `Update()` 函数中调用 `SetAnimation()`，确保每帧都更新动画状态。
6. **设置 Float 参数：**  使用 `anim.SetFloat("velocityX", Mathf.Abs(rb.velocity.x));` 设置 Animator 中 "velocityX" 参数的值，使用 `Mathf.Abs()` 获取 Rigidbody 2D 水平速度的绝对值。
7. **获取 Rigidbody2D 组件：**  在 `PlayerAnimation` 脚本中获取 Rigidbody2D 组件的引用，用于获取速度。

**九、创建 Walk 动画：**

1. **创建新的 Animation Clip：**  在 Animation 窗口创建新的 Animation Clip，命名为 "blue\_walk"，保存到 "Animations/Player" 文件夹。
2. **导入动画帧：**  导入 Walk 动画对应的图片帧 (0-7 来自第二个图集)。
3. **设置 Sample Rate：**  将 Walk 动画的 Sample Rate 设置为 14。
4. **修复 Walk 动画帧：**  发现遗漏了最后两帧 (8 和 9)，将其添加到 "blue\_walk" 动画 Clip 中。

**十、在 Animator 中添加 Walk 状态和 Transition：**

1. **创建 Walk 状态：**  在 Animator 窗口中创建一个新的 State，命名为 "Walk"，并将 "blue\_walk" 动画 Clip 赋给它。
2. **创建 Transition：**

    * Idle to Walk：Condition "velocityX Greater than 0.1"。
    * Walk to Idle：Condition "velocityX Less than 0.1"。
    * Walk to Run：Condition "velocityX Greater than 2.5"。
    * Run to Walk：Condition "velocityX Less than 2.5"。
    * Idle to Run：Condition "velocityX Greater than 2.5"。

**总结来说，这个视频详细介绍了如何在 Unity 中使用帧动画技术创建角色的 Idle、Run 和 Walk 动画，并使用 Animator 组件来管理这些动画状态之间的切换。通过设置 Float 类型的参数 &quot;velocityX&quot; 并根据角色的水平移动速度来控制动画的播放，实现了基本的角色动画控制。**