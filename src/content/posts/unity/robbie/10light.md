---
title: Untity学习记录-2D平台跳跃游戏-10-灯光效果详解：打造沉浸式游戏场景
published: 2025-03-16
tags: ['Unity','游戏']
description: 学习Unity
image: https://cdn.jsdelivr.net/gh/Flandre923/CDN@latest/img/20250316134318.png
category: Unity
draft: false
---
# Unity 灯光效果详解：打造沉浸式游戏场景

## 代码错误修正

首先，我们要修正一个代码错误。在之前的视频中，我们修复了下蹲跳跃的 bug，但判断条件的位置写错了。感谢小伙伴们的提醒，正确的写法是将 `isHeadBlocked` 放在上面，并删除重复的 `isOnGround` 判断。

```csharp
// 正确的判断条件
if (!isHeadBlocked && isOnGround && crouchHeld)
{
    // ...
}
```

## 添加火炬灯光

1. 打开 Props 文件夹，找到 Wall Torch 预制体，并打开预制体进行编辑。
2. 调整预制体的 Sorting Layer 为 Background。
3. 调整火焰粒子效果的 Sorting Layer 为 Background。
4. 将 Wall Torch 预制体拖拽到场景中。
5. 为火炬添加 Point Light 组件。
6. 调整 Point Light 的参数：

    * Z 轴：-0.8，确保灯光在图层上方。
    * 范围：20，调整灯光照射范围。
    * 颜色：偏黄色，模拟火炬光效。

## 添加材质和法线贴图

1. 为 Platform Tilemap 添加 Platform 材质球，使其具有 3D 效果。
2. 为 Background 和 Background Details Tilemap 添加相同的材质球。
3. 调整 Shadow Tilemap 的层级，使其正确遮挡游戏角色。

## 添加背景细节光效

1. 将控制背景泛光的脚本拖拽到 Background Details Tilemap 中。
2. 设置脚本参数，使背景 Unity Logo 具有渐隐渐现的效果。

## 添加悬挂灯光

1. 将 Hanging Light 预制体拖拽到场景中。
2. 观察悬挂灯光的十字形阴影效果，这是通过添加 Cookie 实现的。
3. 观察悬挂灯光的摇摆效果，这是通过调整 Z 轴旋转实现的。

## 调整场景整体光照

1. 打开 Window -\> Rendering -\> Lighting Settings。
2. 调整主场景的环境光颜色和曝光度，使场景更亮。
3. 根据游戏需求，调整场景光照颜色，实现特殊效果。

## 注意事项

* 在电脑端游戏中，可以添加更多灯光和粒子效果。
* 在移动端游戏中，过多的粒子效果会降低游戏性能。
* 根据游戏需求，合理调整场景整体光照。

## 总结

通过本教程，我们学习了如何在 Unity 中添加各种灯光效果，包括火炬灯光、悬挂灯光、背景细节光效和场景整体光照。希望本教程能够帮助你打造更具沉浸感的游戏场景。

