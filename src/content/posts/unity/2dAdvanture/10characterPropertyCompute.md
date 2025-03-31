---
title: Untity学习记录-实现一个完整的2D游戏Demo-10-人物属性及伤害计算
published: 2025-03-31
tags: ['Unity','游戏','demo','2d']
description: 使用Untiy时间一个2D冒险游戏demo
image: https://cdn.jsdelivr.net/gh/Flandre923/CDN@latest/img/20250331195820.png
category: Unity
draft: false
---
**一、创建敌人 (野猪)：**

* **导入敌人资源：**  从 "enemies/bl" 文件夹导入野猪的素材图。
* **素材图设置：**  将 "Pixels Per Unit" 设置为 16 并应用。使用 Sprite Editor 将素材图切割成独立帧 (4x1 网格)，并将轴心点设置在底部中心。
* **将敌人添加到场景：**  将一个野猪精灵拖拽到层级窗口并重命名为 "Boar"。
* **定位和图层：**  调整野猪在场景中的位置，并在 SpriteRenderer 组件中将其 "Order in Layer" 设置为 1 (低于玩家的 5) 以控制可见性。

**二、为敌人添加物理组件：**

* **Rigidbody 2D：**  添加 Rigidbody 2D 组件，将 "Collision Detection" 设置为 "Continuous"，并冻结 Z 轴旋转。
* **Box Collider 2D：**  添加 Box Collider 2D 并调整其大小以适应野猪的脚部区域，用于地面检测。

**三、处理玩家与敌人的碰撞和伤害检测：**

* **初始碰撞问题：**  观察到玩家可以推动野猪。
* **使用触发器：**  引入使用触发器碰撞体 (勾选 "Is Trigger") 进行伤害检测的概念，无需物理碰撞。
* **基于图层的碰撞排除：**

  * 在图层管理器中创建 "Player" 和 "Enemy" 图层。
  * 将 Player 对象的图层设置为 "Player"，将 Boar 对象的图层设置为 "Enemy"。
  * 在 Boar 的 Box Collider 2D 中使用 "Exclude Layers" 排除与 "Player" 和 "Enemy" 图层的碰撞。
* **添加触发器碰撞体 (Capsule Collider 2D)：**  为 Boar 添加一个 Capsule Collider 2D，将其 "Direction" 设置为 "Horizontal"，调整其大小，并启用 "Is Trigger"。从该触发器中排除 "Enemy" 图层以防止自身触发。
* **检测触发器事件 (**​**​`OnTriggerStay2D()`​**​ **):**  在 `PlayerController` 脚本中使用 `OnTriggerStay2D()` 函数检测玩家的碰撞体何时与野猪的触发器碰撞体重叠。输出另一个碰撞体的名称以进行测试。

**四、实现基本角色属性 (Character 脚本)：**

* **创建通用文件夹：**  创建一个名为 "general" 的文件夹用于存放可重用的脚本。
* **移动 PhysicsCheck：**  将 `PhysicsCheck` 脚本移动到 "general" 文件夹。
* **创建 Character 脚本：**  在 "general" 文件夹中创建一个新的 "Character" 脚本来存储生命值信息。
* **添加 Character 脚本：**  将 "Character" 脚本附加到 Player 和 Boar 对象。
* **生命值变量：**  在 `Character` 脚本中声明 `public int maxHealth` 和 `public int currentHealth`。
* **设置初始生命值：**  在 Inspector 中为 Player 和 Boar 设置初始的 `maxHealth` 和 `currentHealth` 值。

**五、实现攻击和伤害 (Attack 脚本和 TakeDamage 函数)：**

* **创建 Attack 脚本：**  在 "general" 文件夹中创建一个新的 "Attack" 脚本来处理攻击伤害。
* **伤害变量：**  在 `Attack` 脚本中声明 `public int damage`。
* **添加 Attack 脚本：**  将 "Attack" 脚本附加到 Player 和 Boar 对象，并设置 Boar 的 `damage` 值。
* **​`TakeDamage()`​**  **函数：**  在 `Character` 脚本中创建一个 `public void TakeDamage(Attack attacker)` 函数来处理伤害减少。
* **应用伤害：**  在 `TakeDamage()` 函数中，从 `currentHealth` 中减去攻击者的伤害。

**六、实现临时无敌：**

* **无敌变量：**  在 `Character` 脚本中声明 `public float invulnerableDuration`、`private float invulnerableCounter` 和 `public bool invulnerable`。
* **​`TriggerInvulnerable()`​**  **函数：**  创建一个 `private void TriggerInvulnerable()` 函数，用于将 `invulnerable` 设置为 true 并重置 `invulnerableCounter`。
* **检查无敌状态：**  在 `TakeDamage()` 函数的开头添加一个检查，如果角色当前处于无敌状态则直接返回。
* **无敌计时器：**  在 `Update()` 函数中实现一个计时器，如果 `invulnerable` 为 true，则按 `Time.deltaTime` 减少 `invulnerableCounter`。当 `invulnerableCounter` 小于或等于零时，将 `invulnerable` 设置回 false。
* **调用** **​`TriggerInvulnerable()`​**​ **：**  在 `TakeDamage()` 函数中应用伤害后调用 `TriggerInvulnerable()` 函数。
* **在 Inspector 中设置无敌持续时间：**  在 Inspector 中为 Player 设置 `invulnerableDuration` (例如 2 秒)。

**七、代码安全性和改进：**

* **空值检查 (语法糖)：**  在概念上的攻击处理中使用空条件运算符 (`?`)，以确保仅当碰撞对象具有 `Character` 组件时才调用 `TakeDamage()`。
* **防止生命值变为负值：**  在 `TakeDamage()` 函数中添加一个检查，确保 `currentHealth` 不会低于零。如果伤害会导致生命值低于零，则直接将 `currentHealth` 设置为零。

总而言之，创建基本敌人、启用使用触发器的非物理碰撞检测，并实现具有临时无敌的初步生命值和伤害系统，为敌人互动奠定了基础。它还强调了代码安全性和正确游戏对象图层的重要性