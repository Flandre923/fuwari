---
title: Untity学习记录-2D平台跳跃游戏-07-射线检测详解：实现精准环境碰撞判断
published: 2025-03-16
tags: ['Unity','游戏']
description: 学习Unity
image: https://cdn.jsdelivr.net/gh/Flandre923/CDN@latest/img/20250316134318.png
category: Unity
draft: false
---
# Unity 射线检测详解：实现精准环境碰撞判断

我们将使用射线来判断游戏角色与周围环境的碰撞关系。为什么要使用射线呢？因为之前的碰撞检测方法存在一些局限性，例如 `isTouchingLayer` 会检测整个碰撞体，导致角色贴墙时也会被判定为在地面上。而使用射线，我们可以实现更精准的碰撞检测，例如分别检测左右脚是否着地，或者检测头顶是否有障碍物。

## RaycastHit2D 结构体

射线检测的核心是 `RaycastHit2D` 结构体。它属于 Unity 的 2D 物理系统，用于存储射线击中物体后的相关信息。我们可以通过 `Physics2D.Raycast` 方法来使用它。

## 代码实现

### 参数设置

首先，我们需要设置一些射线检测相关的参数。

* `footOffset`：左右脚的偏移距离，设置为碰撞体宽度的一半（0.4f）。
* `headDistance`：头顶检测的距离，设置为 0.5f。
* `groundDistance`：地面检测的距离，设置为 0.2f。

### 射线检测方法

我们使用 `Physics2D.Raycast` 方法来创建射线。该方法需要以下参数：

* `origin`：射线的起点。
* `direction`：射线的方向。
* `distance`：射线的长度。
* `layerMask`：射线检测的图层。

### 左右脚检测

我们分别创建两条射线，用于检测左右脚是否着地。

* 左脚射线：

  * 起点：角色位置加上左脚偏移。
  * 方向：`Vector2.down`（向下）。
  * 长度：`groundDistance`。
  * 图层：`groundLayer`。
* 右脚射线：

  * 起点：角色位置加上右脚偏移。
  * 方向：`Vector2.down`（向下）。
  * 长度：`groundDistance`。
  * 图层：`groundLayer`。

### 射线可视化

为了更直观地观察射线，我们使用 `Debug.DrawRay` 方法来绘制射线。该方法需要以下参数：

* `start`：射线的起点。
* `direction`：射线的方向。
* `color`：射线的颜色。
* `length`：射线的长度。

### 方法重载 (Method Overloading)

为了简化代码，我们将射线检测和射线绘制封装到一个重载的 `Raycast` 方法中。该方法接受以下参数：

* `offset`：偏移量。
* `rayDirection`：射线方向。
* `distance`：射线长度。
* `layer`：射线图层。

该方法返回 `RaycastHit2D` 对象，并绘制射线。

### 头顶检测

我们使用重载的 `Raycast` 方法来检测头顶是否有障碍物。

* 起点：角色碰撞体顶部中心点。
* 方向：`Vector2.up`（向上）。
* 长度：`headDistance`。
* 图层：`groundLayer`。

### 碰撞结果可视化

为了更直观地显示碰撞结果，我们根据射线是否击中物体来改变射线的颜色。

* 击中物体：红色。
* 未击中物体：绿色。

### 角色站立判断

在角色站立的逻辑中，我们添加头顶检测的判断，确保角色头顶没有障碍物时才能站立。

## 总结

通过使用射线检测，我们可以实现更精准的环境碰撞判断，提高游戏的交互性和真实感。希望本教程能够帮助你更好地理解和使用 Unity 中的射线检测。

感谢大家的观看，我们下个视频再见！

---

**射线检测的必要性：**

* 传统的碰撞检测方法（如 `isTouchingLayer`）存在局限性，无法满足复杂环境下的精准碰撞判断需求。
* 射线检测可以实现更细粒度的碰撞检测，例如：

  * 分别检测左右脚是否着地。
  * 检测头顶是否有障碍物。

**2.**  **​`RaycastHit2D`​** **结构体：**

* `RaycastHit2D` 是 Unity 2D 物理系统中的一个结构体，用于存储射线击中物体后的相关信息。
* 通过 `Physics2D.Raycast` 方法可以使用它。

**3. 代码实现：**

* **参数设置：**

  * `footOffset`：左右脚偏移距离（碰撞体宽度的一半）。
  * `headDistance`：头顶检测距离。
  * `groundDistance`：地面检测距离。
* **射线检测方法：**

  * 使用 `Physics2D.Raycast` 方法创建射线。
  * 该方法需要以下参数：

    * `origin`：射线起点。
    * `direction`：射线方向。
    * `distance`：射线长度。
    * `layerMask`：射线检测图层。
* **左右脚检测：**

  * 分别创建两条射线，检测左右脚是否着地。
  * 射线参数：

    * 起点：角色位置 + 偏移量。
    * 方向：`Vector2.down`。
    * 长度：`groundDistance`。
    * 图层：`groundLayer`。
* **射线可视化：**

  * 使用 `Debug.DrawRay` 方法绘制射线，便于观察。
  * 该方法需要以下参数：

    * `start`：射线起点。
    * `direction`：射线方向。
    * `color`：射线颜色。
    * `length`：射线长度。
* **方法重载 (Method Overloading)：**

  * 将射线检测和绘制封装到重载的 `Raycast` 方法中，简化代码。
  * 方法参数：

    * `offset`：偏移量。
    * `rayDirection`：射线方向。
    * `distance`：射线长度。
    * `layer`：射线图层。
  * 方法返回 `RaycastHit2D` 对象，并绘制射线。
* **头顶检测：**

  * 使用重载的 `Raycast` 方法检测头顶障碍物。
  * 射线参数：

    * 起点：碰撞体顶部中心点。
    * 方向：`Vector2.up`。
    * 长度：`headDistance`。
    * 图层：`groundLayer`。
* **碰撞结果可视化：**

  * 根据射线是否击中物体，改变射线颜色：

    * 击中：红色。
    * 未击中：绿色。
* **角色站立判断：**

  * 在站立逻辑中添加头顶检测判断，确保头顶无障碍物。

**4. 核心概念：**

* **射线检测：**

  * 使用射线模拟光线传播，检测物体碰撞。
* **结构体：**

  * `RaycastHit2D` 存储碰撞信息。
* **方法重载：**

  * 简化代码，提高代码复用性。
* **可视化调试：**

  * `Debug.DrawRay` 便于调试和观察。