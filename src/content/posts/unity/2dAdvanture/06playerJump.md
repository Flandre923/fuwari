---
title: Untity学习记录-实现一个完整的2D游戏Demo-06-实现人物的跳跃
published: 2025-03-31
tags: ['Unity','游戏','demo','2d']
description: 使用Untiy时间一个2D冒险游戏demo
image: https://cdn.jsdelivr.net/gh/Flandre923/CDN@latest/img/20250331195820.png
category: Unity
draft: false
---
讲解了如何在 Unity 中实现角色的跳跃功能，并使用了新的 Input System 来监听跳跃按键。

**一、设置跳跃按键 (Input System)：**

1. **编辑 Input Actions 资源：**  打开之前创建的 "PlayerInputControl" Input Actions 资源。
2. **创建 Jump Action：**  在 "Gameplay" Action Map 中点击 "+" 按钮创建一个新的 Action，命名为 "Jump"。
3. **Action Type：**  "Jump" Action 的 Action Type 设置为 "Button"。
4. **添加键盘绑定：**  点击 "Jump" Action 下的 "+" 按钮添加一个键盘绑定，点击 "Listen"，按下空格键，选择识别到的 "Space" 键 (Keyboard/Mouse 模板)。
5. **添加手柄绑定：**  再次点击 "+" 按钮添加一个新的绑定，使用 "Listen" 功能，按下手柄下方的按钮 (PS4 的 Cross，Xbox 的 B 等)，选择识别到的通用按钮 (Button South)。
6. **保存 Input Actions：**  强调使用后要保存 Input Actions 资源 (可以勾选 "Auto Save")。

**二、创建跳跃力变量：**

1. **声明 jumpForce 变量：**  在 `PlayerController` 脚本中创建一个 `public float jumpForce` 变量，用于控制跳跃的高度。
2. **使用 Header 特性分组变量：**  使用 `[Header("Basic Parameters")]` 特性将 `speed` 和 `jumpForce` 变量在 Inspector 窗口中分组显示，方便管理。

**三、使用 Rigidbody2D.AddForce() 实现跳跃：**

1. **选择合适的方法：**  区分使用 `Rigidbody2D.velocity` (用于持续移动) 和 `Rigidbody2D.AddForce()` (用于施加力，如跳跃) 的场景。
2. **查阅 Unity Scripting API：**  鼓励查看 Unity Scripting API 中关于 `Rigidbody2D.AddForce()` 的说明和用法。
3. **使用 ForceMode2D.Impulse：**  介绍了 `ForceMode2D.Impulse`，它会施加一个瞬时的力，适合用于跳跃。
4. **实现 Jump 函数：**  创建一个新的 `void Jump()` 函数来执行跳跃逻辑。
5. **施加向上的力：**  使用 `rb.AddForce(transform.up * jumpForce, ForceMode2D.Impulse);` 向 Rigidbody 2D 施加一个向上的瞬时力。`transform.up` 代表世界坐标系的上方。

**四、处理跳跃输入 (Input System)：**

1. **访问 Jump Action：**  通过 `inputControl.Gameplay.Jump` 访问 "Jump" Action。
2. **使用 started 事件：**  解释了 Input Action 的 `started` 事件在按键被按下的那一刻触发一次，适合用于单次执行的动作，如跳跃。
3. **注册回调函数：**  使用 `+=` 运算符将 `Jump()` 函数注册到 "Jump" Action 的 `started` 事件上：`inputControl.Gameplay.Jump.started += Jump;`。
4. **创建 Jump 回调函数：**  演示了如何使用代码编辑器的快速修复功能自动生成 `Jump()` 回调函数，该函数会接收一个 `InputAction.CallbackContext` 参数。

**五、调试跳跃功能：**

1. **使用 Debug.Log()：**  使用 `Debug.Log("Jump");` 在控制台输出信息，用于调试和确认 `Jump()` 函数是否被正确调用。

**六、测试和调整跳跃参数：**

1. **设置 jumpForce：**  在 Inspector 窗口中为 `jumpForce` 设置一个合适的数值 (例如 16.5)。
2. **调整 Gravity Scale：**  修改 Rigidbody 2D 组件的 `Gravity Scale` (例如设置为 4) 来调整重力，从而影响跳跃的手感。

**七、后续需要解决的问题：**

1. **无限跳跃：**  目前角色可以在空中无限次跳跃，需要添加接地检测来限制跳跃次数。
2. **空中移动：**  角色在跳跃时仍然可以水平移动，可以根据需求进行调整。
3. **贴墙问题：**  跳跃时按住方向键可能会导致角色卡在墙上，需要在后续解决。

**八、再次强调查阅代码手册的重要性：**

1. 鼓励养成查看 Unity Scripting API 的习惯，了解组件和函数的使用方法和示例。