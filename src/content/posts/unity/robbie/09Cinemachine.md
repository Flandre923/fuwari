---
title: Untity学习记录-2D平台跳跃游戏-09-摄像机调整与 Cinemachine 透视效果
published: 2025-03-16
tags: ['Unity','游戏']
description: 学习Unity
image: https://cdn.jsdelivr.net/gh/Flandre923/CDN@latest/img/20250316134318.png
category: Unity
draft: false
---
# Unity 摄像机调整与 Cinemachine 透视效果

## 摄像机基础调整

### 背景颜色

首先，为了避免在游戏角色移动到屏幕边界时露出难看的背景颜色，我们选择 Camera，将其 Background 颜色调整为黑色或您喜欢的颜色。

### 透视效果

为了使场景具有透视效果，我们选择 Camera，将 Projection 从 Orthographic 修改为 Perspective。

* **Orthographic（正交投影）：** 摄像机为长方体，无论位置如何，都是垂直拍摄，没有近大远小的效果。
* **Perspective（透视投影）：** 模拟真实摄像机，具有聚焦点和景深效果，产生近实远虚的视觉效果。

### 景深调整

由于初始状态下图层都在同一平面上，没有景深效果，我们需要调整不同 Tilemap 的 Z 轴，以实现景深。

* 将 Background 的 Z 轴设置为 1。
* 将 Background Details 的 Z 轴设置为 0.99。
* 将 Shadow 的 Z 轴设置为 -0.01。

## Cinemachine 设置

### 安装 Cinemachine

为了实现摄像机跟随游戏角色，我们需要安装 Cinemachine。

1. 打开 Window -> Package Manager。
2. 在 All packages 中搜索 Cinemachine。
3. 选择 Cinemachine 并安装。

### 创建 2D 摄像机

1. 在菜单栏选择 Cinemachine -> Create 2D Camera。
2. 将 Cinemachine Virtual Camera 的 Follow 选项设置为游戏角色（Robbie）。
3. 调整 Camera Distance，使场景看起来更大。

### 添加 Confiner 扩展

为了限制摄像机移动范围，我们添加 Confiner 扩展。

1. 在 Cinemachine Virtual Camera 中添加 Extension -> Cinemachine Confiner。
2. 创建一个空物体（Camera Bounds），并添加 Polygon Collider 2D 组件。
3. 将 Polygon Collider 2D 设置为 Trigger。
4. 将 Camera Bounds 拖拽到 Confiner 的 Bounding Shape 选项中。

### 调整边界

1. 选择 Camera Bounds，点击 Edit Collider 调整多边形边界。
2. 按住 Ctrl 点击顶点或边线，可以删除。
3. 根据游戏场景需求，调整边界形状。

### 阴影层级调整

为了使阴影正确遮挡游戏角色，将阴影的 Sorting Layer 设置为 Foreground。

## 总结

通过以上步骤，我们成功调整了摄像机，实现了跟随游戏角色和透视效果，并限制了摄像机的移动范围。希望本教程能够帮助你更好地理解和使用 Unity 中的摄像机和 Cinemachine。

感谢大家的观看，我们下个视频再见！


---

**1. 摄像机基础调整：**

* **背景颜色：**

  * 为了避免在游戏角色移动到屏幕边界时露出难看的背景颜色，需要调整 Camera 的 Background 颜色。
* **透视效果：**

  * 通过将 Camera 的 Projection 从 Orthographic 修改为 Perspective，实现透视效果。

    * **Orthographic（正交投影）：**  没有近大远小的效果，适合 2D 游戏。
    * **Perspective（透视投影）：**  模拟真实摄像机，具有景深效果，适合 3D 游戏。
* **景深调整：**

  * 通过调整不同 Tilemap 的 Z 轴，实现景深效果。

**2. Cinemachine 设置：**

* **安装 Cinemachine：**

  * 通过 Package Manager 安装 Cinemachine 插件，实现摄像机跟随和边界限制等功能。
* **创建 2D 摄像机：**

  * 使用 Cinemachine 创建虚拟摄像机，并设置 Follow 选项，使其跟随游戏角色。
  * 调整Camera Distance，扩大可视的场景范围。
* **添加 Confiner 扩展：**

  * 添加 Confiner 扩展，限制摄像机的移动范围。
  * 创建 Camera Bounds 空物体，并添加 Polygon Collider 2D 组件，设置为 Trigger。
  * 将 Camera Bounds 拖拽到 Confiner 的 Bounding Shape 选项中。
* **调整边界：**

  * 通过编辑 Polygon Collider 2D，调整摄像机的边界形状。
* **阴影层级调整：**

  * 为了使阴影正确的遮挡游戏角色，将阴影的 Sorting Layer 设置为 Foreground。

**3. 核心概念：**

* **摄像机投影模式：**

  * Orthographic 和 Perspective 的区别和应用场景。
* **Cinemachine：**

  * Unity 的摄像机控制插件，提供多种摄像机行为和效果。
* **Collider 边界：**

  * 使用 Collider 限制摄像机的移动范围。
* **层级控制：**

  * 通过调整层级，来正确显示图像的前后遮挡关系。