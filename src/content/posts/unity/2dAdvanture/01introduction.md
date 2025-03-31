---
title: Untity学习记录-实现一个完整的2D游戏Demo-01-场景绘制和叠层
published: 2025-03-31
tags: ['Unity','游戏','demo','2d']
description: 使用Untiy时间一个2D冒险游戏demo
image: https://cdn.jsdelivr.net/gh/Flandre923/CDN@latest/img/20250331195820.png
category: Unity
draft: false
---
本博客是对于[勇者传说](https://www.bilibili.com/video/BV1uZ421U7Qz/?spm_id_from=333.1387.upload.video_card.click)教程的学习笔记。


* **场景素材的准备和编辑：**

  * 导入和选择场景素材图片（例如 forest1 和 forest2）。
  * 了解素材中包含的元素（平台、水、物体等）。
  * 对素材进行设置，例如更改 `Multiple Pixel Per Unit` 为 16，`Filter Mode` 为 `Point (no filter)`，`Compression` 为 `None`。
  * 使用 Sprite Editor 对素材进行切割（Slice），将一张大的场景图分割成多个小的瓦片（tiles），切割模式选择 `Grid By Cell Size` 并设置为 16x16。
  * 调整 Sprite 的锚点（Pivot）到 `Center`，以确保瓦片绘制在正确的格子中。
* **使用 Tile Palette 绘制场景：**

  * 打开 Tile Palette 窗口（Window -\> 2D -\> Tile Palette）。
  * 创建新的 Tile Palette，并将其保存在项目文件夹的指定位置（例如 Assets/Tilemap/Palettes）。
  * 将切割好的场景素材图片拖拽到 Tile Palette 中，并指定瓦片资源的保存位置（例如 Assets/Tilemap/Tiles）。
  * 在 Hierarchy 窗口中创建 Tilemap 对象（+ -\> 2D Object -\> Tilemap -\> Rectangular）。
  * 选择 Tile Palette 中的瓦片，然后在 Scene 视图中使用笔刷工具在 Tilemap 上绘制场景。
  * 可以使用矩形工具进行快速绘制，按住 Shift 键使用矩形工具可以进行擦除。
  * 可以使用 Tile Palette 窗口中的额外工具进行瓦片的镜像和旋转。
* **场景层级和叠层的概念：**

  * 理解 2D 游戏中的叠层（Sorting Layer）概念，用于控制不同游戏对象的前后显示顺序。
  * 通过 Sprite Renderer 组件中的 Sorting Layer 和 Order in Layer 属性来调整对象的叠层关系。
  * 可以创建多个 Sorting Layer（例如 Back, Middle, Front）来更好地组织场景元素。
  * 在同一 Sorting Layer 中，Order in Layer 数值越小，对象越靠后显示；数值越大，对象越靠前显示。
  * 创建多个 Tilemap 对象来绘制不同层级的场景元素（例如 Platform, Back1, Back2, Back3, Front1, Front2, Front3）。
  * 为不同的 Tilemap 设置不同的 Sorting Layer 和 Order in Layer，以实现正确的叠层效果。
  * 使用 Scene 视图右下角的 Focus On 功能（设置为 Tilemap）来方便查看当前正在绘制的 Tilemap 图层。
* **调整游戏画面比例：**

  * 不要通过拖拽 Scene 视图上方的 Skill 滑动条来调整游戏比例，这只会影响编辑器的显示效果。
  * 实际游戏比例可以在 Game 视图左侧的菜单中选择预设的屏幕比例（例如 16:9, 4K, 2K, 1080）。
  * 通过调整 Main Camera 的 Size 属性来调整游戏在屏幕上显示的范围。
* **调整场景背景颜色：**

  * 在 Inspector 窗口中选择 Environment 设置。
  * 可以修改 Skybox Material 或 Background Color 来改变场景的背景。