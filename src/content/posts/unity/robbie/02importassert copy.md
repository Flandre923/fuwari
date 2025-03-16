---
title: Untity学习记录-2D平台跳跃游戏-02-导入并整理素材
published: 2025-03-16
tags: ['Unity','游戏']
description: 学习Unity
image: https://cdn.jsdelivr.net/gh/Flandre923/CDN@latest/img/20250316134318.png
category: Unity
draft: false
---
# 大家好！让我们一起开始制作这款游戏，导入并整理素材！

## 导入素材与项目设置

打开 Unity，新建一个 2D 项目

我们一起创建一个 2D 项目，命名为 "Robbie Platform"，然后点击创建新项目。

创建好新项目后，我们要将素材包导入进来。请大家到屏幕上方的菜单，点击 "Assets" 菜单，在 "Assets" 菜单中选择 "Import Package"，然后选择 "Custom Package"，也就是我们的个人程序包。点选下载好的程序包，点击打开。

我们可以看到导入的内容，选择全部导入，点击导入，等待一段时间后，素材就导入到我们的项目中了。

## 素材概览与解释

素材导入好了，首先要解释的是，这个素材不包括已经写好的脚本，因为我希望大家能够学会自己编写，这样才能完全明白和理解。如果已有基础的朋友想直接查看官方脚本，可以单独发站内信给我，我会单独发送。总之，我们的目标是：自己编写一套可玩性强的脚本！

我们来看看素材都包括什么内容：

* **Assets/Extras**: 包含一些额外的预制件（Prefabs），将在游戏后期使用。
* **Assets/Addons**: 包含插件，包括 TextMesh Pro（UI 字幕）和 Tilemap 2D 扩展包（官方提供）。
* **Assets/Audio**: 所有音乐素材。
* **Assets/Fonts**: 我们要用到的字体。
* **Assets/Plugins/Cinemachine**: 2D 摄像机跟踪插件。
* **Assets/Level**: 包含 Sprites（Tilemap 素材、背景素材）、Materials（背景材质球）、Prefabs（游戏元素预制件）、Robbie（主角模型、骨骼模型）。
* **Assets/Scenes**: 默认场景。
* **Assets/Scripts**: 包含简单的代码，我们之后会自己编写所需代码。
* **Assets/Tilemap**: 空文件夹，用于整理 Tilemap 素材。
* **Assets/UI**: 所有 UI 元素。
* **Assets/VFX**: 视觉特效。

## 导入 Tilemap 素材与创建 Palette

了解素材内容后，我们来导入 Tilemap 素材，也就是 "Assets/Level/Sprites" 中的三个素材。

首先，我们要打开 Tile Palette（瓦片调色板）。点击菜单 "Window" -> "2D" -> "Tile Palette"，将窗口放到合适的位置。

点击 "Create New Palette"，创建新的调色板。我们先创建背景相关的，命名为 "Background"，点击创建。选择保存位置，在 "Assets/Tilemap" 文件夹中创建 "Palettes" 文件夹，然后选择。

将切好的 Tilemap 素材拖拽到 Palette 中，选择保存瓦片的位置，在 "Assets/Tilemap/Tiles" 文件夹中创建 "Background" 文件夹，然后选择。

同样的方法，将其他两个素材也导入，分别创建 "Platform" 和 "Shadow" 两个 Palette。

## Tile Palette 的使用与场景绘制

现在，我们可以通过下拉菜单选择要使用的 Palette。

在 Tile Palette 中，点击 "Edit" 按钮可以调整 Palette 中的 Tile，移动元素位置。取消 "Edit" 按钮后，才能在游戏场景中绘制。

如果背景素材缺失部分 Tile，可以从 "Assets/Tilemap/Tiles" 文件夹中拖入补充。

在 Hierarchy 窗口中，新建 Tilemap 进行绘制。点击左上角新建或右键选择 "2D" -> "Tilemap"，命名为 "Background"。可以创建多个 Tilemap 存放不同内容。

选中 Tilemap，在 "Active Tilemap" 中选择要绘制的 Tilemap，然后在场景中开始绘制。