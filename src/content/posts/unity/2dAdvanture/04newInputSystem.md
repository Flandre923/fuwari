---
title: Untity学习记录-实现一个完整的2D游戏Demo-04-创建及配置新的输入系统 new input system
published: 2025-03-31
tags: ['Unity','游戏','demo','2d']
description: 使用Untiy时间一个2D冒险游戏demo
image: https://cdn.jsdelivr.net/gh/Flandre923/CDN@latest/img/20250331195820.png
category: Unity
draft: false
---
**一、创建和组织代码文件：**

1. **创建 Scripts 文件夹：**  为了更好地组织项目文件，首先在 Project 窗口的 Assets 目录下创建一个名为 "Scripts" 的新文件夹。
2. **创建 Player 子文件夹：**  在 "Scripts" 文件夹下创建一个子文件夹，用于存放与玩家角色相关的脚本，命名为 "Player"。
3. **创建第一个 C# 脚本：**

    * 在 "Player" 文件夹中，点击 "+" 按钮，选择 "C# Script"。
    * **重要：**  在创建时立即为脚本命名，例如 "PlayerController"。
4. **脚本命名与类名一致性：**  强调脚本的文件名必须与脚本内部定义的类名（`public class PlayerController : MonoBehaviour`）完全一致，否则会导致代码无法正确使用。
5. **将脚本添加到 Player 对象：**

    * 选中 Hierarchy 窗口中的 Player 对象。
    * 可以通过以下两种方式将 "PlayerController" 脚本添加到 Player 对象上：

      * 将脚本从 Project 窗口拖拽到 Player 对象上。
      * 在 Inspector 窗口中点击 "Add Component"，搜索并添加 "PlayerController" 脚本。
    * 说明脚本也像其他 Unity 内置组件一样，可以被添加到游戏对象上以赋予其特定的功能。
6. **命名规范 (Camel Case)：**  介绍了在代码中使用的命名规范：

    * **PascalCase (大驼峰)：**  用于类名（例如 `PlayerController`），每个单词首字母大写。
    * **camelCase (小驼峰)：**  用于变量名（例如 `inputDirection`），第一个单词首字母小写，后续单词首字母大写。

**二、熟悉基本的 Unity 脚本结构：**

1. **打开脚本编辑器：**  双击 Project 窗口中的 "PlayerController" 脚本以打开代码编辑器（示例中使用的是 Visual Studio Code）。
2. **基本的代码模板：**  介绍了 Unity C# 脚本的基本结构：

    * **​`using UnityEngine;`​**​ **：**  导入 Unity 引擎的命名空间，允许使用 Unity 提供的各种功能。
    * **​`public class PlayerController : MonoBehaviour`​**​ **：**  定义一个名为 `PlayerController` 的公共类，它继承自 `MonoBehaviour`。`MonoBehaviour` 是 Unity 中所有脚本的基础类。
    * **代码周期函数：**

      * **​`Start()`​**​ **：**  在游戏开始的第一帧执行一次。
      * **​`Update()`​**​ **：**  在游戏运行的每一帧都执行。解释了帧率 (FPS) 的概念以及 `Update()` 函数的执行频率。

**三、升级到 Unity 新的输入系统 (Input System Package)：**

1. **原因：**  新的输入系统提供更好的跨平台支持和更灵活的输入管理。
2. **步骤：**

    * **更改 API 兼容级别：**  在 "Edit" -\> "Project Settings" -\> "Player" -\> "Other Settings" 中，将 "API Compatibility Level" 从 ".NET Standard 2.1" 改为 ".NET Framework" 以使用更多 C# 特性。
    * **激活新的输入处理：**  在 "Other Settings" 中，将 "Active Input Handling" 从 "Input Manager (Old)" 改为 "Input System Package (New)"。Unity 会提示重启编辑器。
3. **安装 Input System Package：**

    * 重启 Unity 后，打开 "Window" -\> "Package Manager"。
    * 在 Package Manager 中，选择 "Unity Registry"。
    * 搜索 "Input System" 并点击 "Install" 进行安装。

**四、创建和配置 Input Actions 资源：**

1. **创建 Input System 文件夹：**  在 Project 窗口的 "Settings" 文件夹下创建一个名为 "Input System" 的文件夹。
2. **创建 Input Actions 资源：**  在 "Input System" 文件夹中，点击 "+" 按钮，选择 "Input Actions"，并命名为 "PlayerInputControl"。
3. **编辑 Input Actions：**  双击 "PlayerInputControl" 资源打开配置窗口。
4. **Action Maps：**  介绍了 Action Maps 的概念，用于在不同游戏状态下切换不同的输入配置（例如 "Gameplay" 和 "UI"）。
5. **Actions：**  在 "Gameplay" Action Map 中创建了一个名为 "Movement" 的 Action。
6. **Action Type：**  将 "Movement" Action 的 Action Type 设置为 "Value" -\> "Vector 2" 以处理二维移动输入。
7. **Bindings：**  为 "Movement" Action 添加了键盘 (W, A, S, D) 和手柄 (Left Stick) 的绑定。
8. **Control Schemes：**  介绍了 Control Schemes 的概念，用于区分不同的输入设备（例如 "Keyboard" 和 "Gamepad"）。
9. **自动保存 (Auto-Save)：**  启用 Auto-Save 以自动保存 Input Actions 的配置。

**五、生成 C# 类以访问 Input Actions：**

1. **Generate C# Class：**  选中 "PlayerInputControl" 资源，在 Inspector 窗口中勾选 "Generate C# Class" 选项，并点击 "Apply"。这会自动生成一个 C# 脚本文件，用于在代码中访问 Input Actions。

**六、在 PlayerController 脚本中使用新的输入系统：**

1. **导入命名空间：**  在 "PlayerController" 脚本中添加 `using UnityEngine.InputSystem;` 命名空间。
2. **声明 Input Actions 变量：**  声明一个公共变量来引用生成的 Input Actions 类：`public PlayerInputControl inputControl;`。
3. **实例化 Input Actions：**  在 `Awake()` 函数中实例化 `inputControl` 对象：`inputControl = new PlayerInputControl();`。
4. **启用和禁用 Input Actions：**

    * 在 `OnEnable()` 函数中启用 Input Actions：`inputControl.Enable();`。
    * 在 `OnDisable()` 函数中禁用 Input Actions：`inputControl.Disable();`。
5. **读取输入值：**

    * 声明一个 `Vector2` 类型的公共变量 `inputDirection` 来存储移动输入值。
    * 在 `Update()` 函数中，使用 `inputControl.Gameplay.Move.ReadValue<Vector2>();` 读取 "Gameplay" Action Map 中 "Move" Action 的 Vector 2 值，并将其赋值给 `inputDirection` 变量。
6. **在编辑器中观察输入值：**  运行游戏，可以在 Inspector 窗口中看到 `inputDirection` 变量随着键盘按键或手柄摇杆的输入而变化。