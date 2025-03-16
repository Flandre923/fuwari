---
title: Untity学习记录-2D平台跳跃游戏-08-实现悬挂效果：射线检测与状态控制
published: 2025-03-16
tags: ['Unity','游戏']
description: 学习Unity
image: https://cdn.jsdelivr.net/gh/Flandre923/CDN@latest/img/20250316134318.png
category: Unity
draft: false
---
# 学到了

2D情况下，人物在边缘悬挂的实现方法。

悬挂的判定条件。

悬挂状态下的跳跃和下蹲。

---

# Unity 实现悬挂效果：射线检测与状态控制

## 思路分析

我们的目标是当游戏角色在平台边缘下落时，能够停靠在边缘的位置。因此，我们需要：

1. 找到判定悬挂的条件。
2. 解决如何让游戏角色停在那里。

判定悬挂的条件，我们需要参考Unity官方给出的射线判断条件图片。通过图片我们可以分析出以下几点。

* 头顶上没有额外的平台或墙壁。
* 下方有墙壁。
* 游戏角色离墙面的距离不是太远。

## 代码实现

### Collider 调整

首先，在 Unity 中调整 Collider 的 size，将 Y 轴数值变为 1.9，确保游戏角色跳跃时能够够到平台边缘。

### 定义射线变量

在代码中定义所需的射线变量。

* `playerHeight`：游戏角色高度（Collider 的高度）。
* `eyeHeight`：眼睛位置的高度（1.5f）。
* `grabDistance`：游戏角色离墙面的最大距离（0.4f）。
* `reachOffset`：头顶向下射线的距离（0.7f）。
* `isHanging`：判断角色是否正在悬挂。

### 悬挂射线判断

1. **头顶射线：**

    * 起点：游戏角色右上角或左上角。
    * 方向：根据角色朝向（`transform.localScale.x`）确定。
    * 长度：`grabDistance`。
    * 图层：`groundLayer`。
2. **眼睛射线：**

    * 起点：游戏角色眼睛位置。
    * 方向：与头顶射线方向相同。
    * 长度：`grabDistance`。
    * 图层：`groundLayer`。
3. **头顶向下射线：**

    * 起点：游戏角色头顶。
    * 方向：`Vector2.down`（向下）。
    * 长度：`reachOffset`。
    * 图层：`groundLayer`。

### 悬挂条件判断

当满足以下条件时，角色进入悬挂状态：

* 角色不在地面上（`!isOnGround`）。
* 角色正在下落（`rb.velocity.y < 0f`）。
* 头顶向下射线击中物体（`ledgeCheck`）。
* 眼睛射线击中物体（`wallCheck`）。
* 头顶射线未击中物体（`!blockedCheck`）。

### 悬挂状态控制

1. **静止：**

    * 将 `Rigidbody2D` 的 `bodyType` 设置为 `RigidbodyType2D.Static`，使角色静止。
    * 设置 `isHanging` 为 `true`。
2. **位置调整：**

    * 调整角色的 `transform.position`，使其紧贴墙面，并与平台边缘对齐。
    * 在X轴的方向上增加一个小的偏移量，使角色贴图效果更好。
3. **左右移动限制：**

    * 在 `GroundMovement` 函数中，当角色处于悬挂状态时，直接 `return`，阻止左右移动。

### 悬挂状态下的操作

1. **跳跃：**

    * 当角色处于悬挂状态且按下跳跃键时：

      * 将 `Rigidbody2D` 的 `bodyType` 改回 `RigidbodyType2D.Dynamic`。
      * 给角色施加向上的力。
      * 设置 `isHanging` 为 `false`。
2. **下落：**

    * 当角色处于悬挂状态且按下下蹲键时：

      * 将 `Rigidbody2D` 的 `bodyType` 改回 `RigidbodyType2D.Dynamic`。
      * 设置 `isHanging` 为 `false`。

### Bug 修复

* 修复下蹲跳跃时，头顶有障碍物仍然可以跳跃的 bug。

## 总结

通过使用射线检测和状态控制，我们成功实现了悬挂效果。希望本教程能够帮助你更好地理解和使用 Unity 中的射线检测和状态控制。

感谢大家的观看，我们下个视频再见！

---

好的，让我们来总结一下这篇博客文章中关于 Unity 实现悬挂效果的知识点：

**1. 悬挂效果的思路分析：**

* 目标：使游戏角色在平台边缘下落时，能够停靠在边缘位置。
* 判定条件：

  * 头顶上没有额外的平台或墙壁。
  * 下方有墙壁。
  * 游戏角色离墙面的距离不是太远。
* 实现方法：利用射线检测和状态控制。

**2. 代码实现的关键步骤：**

* **Collider 调整：**

  * 调整 `Collider` 的 `size`，确保角色跳跃时能到达平台边缘。
* **定义射线变量：**

  * `playerHeight`：角色高度。
  * `eyeHeight`：眼睛高度。
  * `grabDistance`：最大墙面距离。
  * `reachOffset`：头顶向下射线距离。
  * `isHanging`：悬挂状态标志。
* **悬挂射线判断：**

  * 头顶射线：检测上方是否被阻挡。
  * 眼睛射线：检测前方是否有墙壁。
  * 头顶向下射线：检测下方是否有平台边缘。
* **悬挂条件判断：**

  * 角色不在地面。
  * 角色正在下落。
  * 头顶向下射线击中物体。
  * 眼睛射线击中物体。
  * 头顶射线未击中物体。
* **悬挂状态控制：**

  * **静止：**

    * 设置 `Rigidbody2D.bodyType` 为 `Static`。
    * 设置 `isHanging` 为 `true`。
  * **位置调整：**

    * 调整 `transform.position`，使角色紧贴墙面并对齐边缘。
    * x轴的位置偏移。
  * **左右移动限制：**

    * 在 `GroundMovement` 中，悬挂状态时 `return`。
* **悬挂状态下的操作：**

  * **跳跃：**

    * 恢复 `Rigidbody2D.bodyType`。
    * 施加向上力。
    * 设置 `isHanging` 为 `false`。
  * **下落：**

    * 恢复 `Rigidbody2D.bodyType`。
    * 设置 `isHanging` 为 `false`。
* **Bug 修复：**

  * 修复下蹲跳跃时，头顶有障碍物仍然可以跳跃的 bug。

**3. 核心技术点：**

* **射线检测：**

  * 利用 `Physics2D.Raycast` 进行环境检测。
* **状态控制：**

  * 使用布尔变量 `isHanging` 管理悬挂状态。
  * 通过修改 `Rigidbody2D.bodyType` 控制角色物理行为。
* **位置控制：**

  * 通过修改transform.position，来控制角色的位置。