---
title: Untity学习记录-2D平台跳跃游戏-06-实现多重跳跃效果
published: 2025-03-16
tags: ['Unity','游戏']
description: 学习Unity
image: https://cdn.jsdelivr.net/gh/Flandre923/CDN@latest/img/20250316134318.png
category: Unity
draft: false
---
## 参数调整

首先，我们需要调整一些参数。在上一个视频中，我们为游戏角色添加了 Collider，并调整了一些 Collider 参数。但我们发现，之前的参数导致游戏角色下半部分的 Collider 没有触碰到脚底的位置。因此，我们需要修改这部分参数。

* 将 Collider 的 Offset 参数改为 0.9，使 Collider 能够顺利到达游戏角色的最底部。
* 修改重力参考参数。注意，这里修改的不是 Rigidbody2D 中的重力比例，而是系统设置的重力基数。

  * 进入 Unity 的 Edit 窗口，找到 Project Settings，选择 Physics 2D。
  * 将重力数值的 Y 轴数值改为 -50。

## 代码编写

完成基本参数修改后，我们开始编写代码。打开 VS 进行代码编写。

### 跳跃相关参数

首先，设置一些与跳跃相关的参数。

* `jumpForce`：基本跳跃力度，设置为 6.3f。
* `jumpHoldForce`：长按跳跃键时的额外加成力度。
* `jumpHoldDuration`：长按跳跃键的最长时间，设置为 0.1f（稍后解释原因）。
* `crouchJumpBoost`：下蹲状态下的额外跳跃加成。
* `jumpTime`：用于计算跳跃时间的变量，与 `jumpHoldDuration` 配合使用。

### 角色状态

在状态部分添加额外的状态。

* `isOnGround`：判断角色是否站在地面上。
* `isJump`：判断角色是否正在跳跃。

### 环境检测

添加环境检测参数。

* `groundLayer`：地面图层（LayerMask）。

### 按键设置

为了使跳跃和移动更加顺滑，我们修改按键的判断方式。

* 使用一系列布尔值来接收键盘输入。

  * `jumpPressed`：单次按下跳跃键。
  * `jumpHeld`：长按跳跃键。
  * `crouchHeld`：长按下蹲键。
* 在 `Update` 方法中为这些布尔值赋值。

### 物理环境判断

在 `GroundMovement` 之前，添加物理环境判断。

* 使用 `Collider2D.IsTouchingLayers` 方法判断角色是否在地面上。
* 根据判断结果设置 `isOnGround` 的值。

### 跳跃代码实现

创建 `MidAirMovement` 函数，实现跳跃逻辑。

* 判断条件：

  * 单次按下跳跃键 (`jumpPressed`)。
  * 角色在地面上 (`isOnGround`)。
  * 角色不在跳跃状态 (`!isJump`)。
* 执行操作：

  * 设置 `isOnGround` 为 `false`。
  * 设置 `isJump` 为 `true`。
  * 使用 `rb.AddForce` 方法给角色添加向上的力，实现跳跃。
* 长按跳跃：

  * 判断条件：

    * 角色在跳跃状态 (`isJump`)。
    * 长按跳跃键 (`jumpHeld`)。
  * 执行操作：

    * 使用 `rb.AddForce` 方法给角色添加额外的向上力。
* 跳跃时间控制：

  * 在跳跃开始时，使用 `Time.time` 记录跳跃开始时间，并加上 `jumpHoldDuration`。
  * 在 `Update` 方法中，判断当前时间是否超过跳跃结束时间，如果超过，则设置 `isJump` 为 `false`，结束跳跃。

### 下蹲跳跃

在下蹲状态下跳跃时，添加额外的跳跃增量。

* 在 `Crouch` 和 `StandUp` 函数中，添加对 `isOnGround` 的判断，确保在空中时能够正确切换 Collider。
* 在跳跃代码中，判断角色是否处于下蹲状态和地面上，如果是，则：

  * 使用 `StandUp` 函数恢复 Collider。
  * 使用 `rb.AddForce` 方法给角色添加 `crouchJumpBoost` 增量。

## 代码总结

通过以上步骤，我们实现了多重跳跃效果，包括普通跳跃、长按跳跃和下蹲跳跃。这些代码的逻辑关系可能需要一些时间来理解，建议您将代码复制到 Unity 中，并修改参数进行实验，以便更好地理解其工作原理。


# 总结

感谢大家的点赞和关注，我们下个视频再见！

**1. 参数调整：**

* **Collider 调整：**

  * 修改 `BoxCollider2D` 的 `Offset` 参数，确保碰撞体能够准确覆盖角色底部，避免碰撞检测误差。
* **重力参数调整：**

  * 在 Unity 的 `Project Settings` 中，修改 `Physics 2D` 的重力 `Y` 轴数值，调整整体重力效果，而非仅修改 `Rigidbody2D` 的重力比例。

**2. 代码编写：**

* **跳跃相关参数：**

  * `jumpForce`：设置基础跳跃力度。
  * `jumpHoldForce`：设置长按跳跃键的额外力度。
  * `jumpHoldDuration`：设置长按跳跃的最长时间，控制跳跃高度。
  * `crouchJumpBoost`：设置下蹲跳跃的额外力度。
  * `jumpTime`：记录跳跃开始时间，用于控制长按跳跃的持续时间。
* **角色状态：**

  * `isOnGround`：判断角色是否在地面上，用于控制跳跃条件。
  * `isJump`：判断角色是否正在跳跃，用于控制长按跳跃和防止重复跳跃。
* **环境检测：**

  * `groundLayer`：设置地面图层，用于 `Collider2D.IsTouchingLayers` 检测。
* **按键设置：**

  * 使用布尔变量 `jumpPressed`、`jumpHeld`、`crouchHeld` 接收键盘输入，提高按键响应的准确性和灵活性。
  * 在 `Update` 函数中更新这些布尔变量。
* **物理环境判断：**

  * 使用 `Collider2D.IsTouchingLayers` 检测角色是否在地面上，更新 `isOnGround` 状态。
* **跳跃代码实现：**

  * `MidAirMovement` 函数实现跳跃逻辑。
  * **普通跳跃：**

    * 判断条件：`jumpPressed`、`isOnGround`、`!isJump`。
    * 操作：设置 `isOnGround` 为 `false`，`isJump` 为 `true`，使用 `rb.AddForce` 添加向上力。
  * **长按跳跃：**

    * 判断条件：`isJump`、`jumpHeld`。
    * 操作：使用 `rb.AddForce` 添加额外向上力。
  * **跳跃时间控制：**

    * 记录跳跃开始时间 `jumpTime = Time.time + jumpHoldDuration`。
    * 判断 `Time.time` 是否超过 `jumpTime`，超过则设置 `isJump` 为 `false`。
* **下蹲跳跃：**

  * 在 `Crouch` 和 `StandUp` 函数中，添加 `isOnGround` 判断，确保空中 Collider 切换正确。
  * 在跳跃代码中，判断下蹲状态和地面状态，使用 `StandUp` 恢复 Collider，并使用 `rb.AddForce` 添加 `crouchJumpBoost`。

**3. 核心概念：**

* **物理引擎：**

  * `Rigidbody2D` 和 `AddForce` 的使用，控制角色物理行为。
* **碰撞检测：**

  * `Collider2D.IsTouchingLayers` 的使用，检测角色与地面的碰撞。
* **输入系统：**

  * `Input.GetButtonDown` 和 `Input.GetButton` 的使用，获取键盘输入。
* **时间控制：**

  * `Time.time` 的使用，控制跳跃持续时间。
* **状态管理：**

  * 使用布尔变量管理角色状态，控制代码逻辑。