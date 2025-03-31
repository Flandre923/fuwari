---
title: Untity学习记录-实现一个完整的2D游戏Demo-12-三段攻击动画的实现
published: 2025-03-31
tags: ['Unity','游戏','demo','2d']
description: 使用Untiy时间一个2D冒险游戏demo
image: https://cdn.jsdelivr.net/gh/Flandre923/CDN@latest/img/20250331195820.png
category: Unity
draft: false
---
**一、玩家攻击的准备：**

1. **分析攻击动画素材：**  在人物素材的 "get" 文件夹中，找到攻击动画帧。第一段攻击有 6 帧（序号 6-11），第二段攻击有 2 帧（序号 12-13），第三段是暴击，有 8 帧（使用 blue 2 图片，序号 22-29）。
2. **创建攻击动画片段：**  在 Player 的文件夹中创建三个 Animation Clip，分别命名为 "attack 1"、"attack 2" 和 "attack 3"，并将对应的动画帧拖拽到时间轴上。

**二、设置攻击动画层：**

1. **创建 Attack Layer：**  在 Animator 窗口中点击 "+" 创建一个新的 Layer，命名为 "attack layer"，并将 Weight 设置为 1。
2. **创建空 State：**  在 "attack layer" 中创建一个空的 State，作为默认的进入点。
3. **添加攻击动画：**  将 "attack 1"、"attack 2" 和 "attack 3" 这三个动画片段拖拽到 "attack layer" 中，按照顺序叠放。

**三、实现三段攻击的切换逻辑：**

1. **创建 Is Attack 布尔参数：**  在 Animator 中创建一个新的 Bool 类型的参数，命名为 "is attack"，用于进入攻击状态。
2. **创建 Attack Trigger 参数：**  创建一个新的 Trigger 类型的参数，命名为 "attack"，用于触发每一段攻击。
3. **创建动画状态之间的 Transition：**

    * 从空 State 到 "attack 1" 的 Transition，条件是 "is attack" 为 true。
    * 从 "attack 1" 到 "attack 2" 的 Transition，条件是触发 "attack"。
    * 从 "attack 2" 到 "attack 3" 的 Transition，条件是触发 "attack"。
    * 从 "attack 1"、"attack 2" 和 "attack 3" 分别创建 Transition 返回空 State，表示一段攻击完整播放结束。
4. **调整 Transition 的 Exit Time：**  将从 "attack 1" 到 "attack 2" 和从 "attack 2" 到 "attack 3" 的 Exit Time 设置为 90%，允许在当前攻击动画播放到 90% 时按下攻击键即可进入下一段连击。

**四、添加攻击输入：**

1. **在 Input System 中添加 Attack Action：**  在 Input System 编辑器中，创建一个新的 Action，命名为 "Attack"，类型为 "Button"。
2. **绑定按键：**  为 "Attack" Action 添加键盘绑定（J 键）和手柄绑定（PS4 手柄的方块键）。

**五、处理攻击输入并触发动画：**

1. **获取 PlayerInput 控制：**  在 `PlayerController` 脚本的 `Awake()` 函数中，获取 PlayerInput 组件。
2. **监听 Attack 按键事件：**  监听 "Attack" Action 的 "started" 事件。
3. **创建 PlayerTag 函数：**  创建一个名为 `PlayerTag()` 的函数来处理攻击相关的逻辑。
4. **获取 PlayerAnimation 组件：**  在 `PlayerController` 脚本中获取 `PlayerAnimation` 组件的引用。
5. **触发 Attack Trigger：**  在 `PlayerTag()` 函数中调用 `PlayerAnimation` 脚本中的 `PlayAttack()` 函数，并在 `PlayAttack()` 函数中使用 `anim.SetTrigger("attack");` 来触发 Animator 中的 "attack" Trigger。
6. **设置 Is Attack 状态：**  在 Animator 中创建一个名为 "is attack" 的 Bool 参数，并在 `PlayerController` 脚本中创建一个对应的 `isAttack` 变量。在 `PlayerTag()` 函数中将 `isAttack` 设置为 `true`，以进入攻击状态。

**六、简化连击逻辑（移除 Combo 计数器）：**

决定通过动画播放时间和攻击触发器来控制连击，不再使用 Combo 计数器。删除 Animator 中的 "combo" Integer 参数以及 `PlayerController` 和 `PlayerAnimation` 脚本中相关的代码。

**七、攻击动画结束后重置 Is Attack 状态：**

1. **创建 AttackFinish 脚本：**  创建一个新的 C# 脚本，命名为 "AttackFinish"，继承自 `StateMachineBehaviour`。
2. **添加到攻击动画状态：**  将 "AttackFinish" 脚本附加到 "attack 1"、"attack 2" 和 "attack 3" 这三个攻击动画状态上。
3. **重写 OnStateExit 函数：**  在 "AttackFinish" 脚本中重写 `OnStateExit()` 函数。
4. **重置 Is Attack 状态：**  在 `OnStateExit()` 函数中，获取 Animator 所在游戏对象的 `PlayerController` 组件，并将 `isAttack` 设置为 `false`。

总而言之，本视频讲解了如何在 Unity 中实现玩家角色的三段攻击连击动画。通过创建独立的动画层、设置触发器和布尔参数，并结合 Input System 来监听玩家的攻击输入，实现了连贯的攻击动画切换。同时，为了简化逻辑，移除了原本计划使用的连击计数器，并使用 `StateMachineBehaviour` 脚本在动画播放结束后重置攻击状态