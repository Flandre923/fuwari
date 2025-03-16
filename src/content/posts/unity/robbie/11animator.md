---
title: Untity学习记录-2D平台跳跃游戏-11-角色动画与材质添加：打造生动游戏角色
published: 2025-03-16
tags: ['Unity','游戏']
description: 学习Unity
image: https://cdn.jsdelivr.net/gh/Flandre923/CDN@latest/img/20250316134318.png
category: Unity
draft: false
---
# Unity 角色动画与材质添加：打造生动游戏角色

## 火炬预制体更新

首先，我们要解决上个视频中忘记的一个步骤：将火炬的点光源添加到预制体中。

1. 选择场景中的火炬。
2. 在 Inspector 窗口的 Prefab 选项中，点击 Overrides -> Apply All。

这样，从 Props 文件夹拖拽火炬到场景中时，它将包含点光源效果。

## 添加角色身体模型

1. 在文件夹中找到 Robbie -> Robbie body 预制体。
2. 将 Robbie body 拖拽到 Robbie 下方，作为其子集。
3. 取消勾选 Robbie 2D 贴图的 Sprite Renderer 组件，显示 Robbie body 模型。

Robbie body 已经包含了法线贴图和骨骼。

## 调整角色层级

1. 观察 Robbie body 在火炬灯光下的显示问题，发现其 Sorting Layer 不正确。
2. 在 Sorting Layers 中，将 Default 图层调整到 Player 图层之前。

这样，Robbie body 就可以正常显示。

## 添加角色动画

### 打开动画窗口

1. 打开 Window -> Animation -> Animation 和 Window -> Animation -> Animator。

### 查看 Animator 控制器

1. 观察 Animator 控制器中的参数和过渡条件。

### 创建 PlayerAnimation 脚本

1. 在 Scripts 文件夹中，创建 PlayerAnimation C# 脚本。
2. 将 PlayerAnimation 脚本拖拽到 Robbie body 上。
3. 打开 PlayerAnimation 脚本进行编辑。

### 获取 Animator 组件

```csharp
private Animator anim;

void Start()
{
    anim = GetComponent<Animator>();
}
```

### 获取 PlayerMovement 组件

C#

```
private PlayerMovement movement;void Start(){    movement = GetComponentInParent<PlayerMovement>();}
```

### 设置动画参数

1. 将 PlayerMovement 中的移动速度和状态信息传递给 Animator。
2. 使用 `Animator.StringToHash` 方法获取动画参数的数字编号，提高性能和稳定性。

C#

```
// 示例：设置移动速度anim.SetFloat(speedID, Mathf.Abs(movement.xVelocity));// 示例：设置布尔值anim.SetBool(groundID, movement.isOnGround);
```

### 添加跳跃动画

1. 在 Assets -\> Robbie -\> Animation -\> MidAir 中，观察跳跃动画。
2. 发现 MidAir 是一个 Blend Tree，使用 verticalVelocity 参数控制动画播放。
3. 调整 Blend Tree 中的动画片段和阈值，使其与角色跳跃速度匹配。
4. 在 PlayerAnimation 脚本中，获取 Rigidbody2D 组件，并将 Y 轴速度传递给 Blend Tree。

C#

```
private Rigidbody2D rb;void Start(){    rb = GetComponentInParent<Rigidbody2D>();}// 设置跳跃动画参数anim.SetFloat(fallID, rb.velocity.y);
```

### 添加动画事件

1. 观察动画片段中的 Add Event，发现其用于播放脚步声音。
2. 后续视频将介绍声音控制，解决动画事件错误。

---

**1. 火炬预制体更新：**

* 将场景中火炬的点光源应用到预制体，确保后续拖拽的火炬包含灯光效果。
* 使用 Inspector 窗口的 Prefab -\> Overrides -\> Apply All 实现。

**2. 添加角色身体模型：**

* 将 Robbie body 预制体作为 Robbie 的子集，显示 3D 角色模型。
* 取消 Robbie 2D 贴图的 Sprite Renderer 组件，隐藏 2D 贴图。
* Robbie body 已包含法线贴图和骨骼。

**3. 调整角色层级：**

* 解决 Robbie body 在灯光下的显示问题，调整其 Sorting Layer。
* 将 Default 图层调整到 Player 图层之前。

**4. 添加角色动画：**

* **打开动画窗口：**

  * 打开 Animation 和 Animator 窗口。
* **查看 Animator 控制器：**

  * 观察动画参数和过渡条件。
* **创建 PlayerAnimation 脚本：**

  * 创建 C# 脚本，并添加到 Robbie body 上。
* **获取 Animator 组件：**

  * 使用 `GetComponent<Animator>()` 获取 Animator 组件。
* **获取 PlayerMovement 组件：**

  * 使用 `GetComponentInParent<PlayerMovement>()` 获取父级 PlayerMovement 组件。
* **设置动画参数：**

  * 将 PlayerMovement 的移动速度和状态信息传递给 Animator。
  * 使用 `Animator.StringToHash()` 获取动画参数的数字编号，提高性能。
  * 使用 `anim.SetFloat()` 和 `anim.SetBool()` 设置动画参数。
* **添加跳跃动画：**

  * 观察 MidAir Blend Tree，了解跳跃动画的控制方式。
  * 调整 Blend Tree 中的动画片段和阈值。
  * 获取 Rigidbody2D 组件，并将 Y 轴速度传递给 Blend Tree。
  * 使用 `anim.SetFloat()` 设置跳跃动画参数。
* **添加动画事件：**

  * 了解动画事件的作用，后续视频将介绍声音控制。

**5. 核心技术点：**

* **预制体更新：**

  * 通过 Overrides -\> Apply All，更新预制体。
* **模型层级控制：**

  * 通过 Sorting Layers，控制模型的显示顺序。
* **Animator 控制器：**

  * 使用 Animator 控制动画状态和过渡。
* **动画参数传递：**

  * 通过脚本，将游戏状态传递给 Animator。
* **Blend Tree：**

  * 使用 Blend Tree，实现复杂的动画过渡。
* **动画事件：**

  * 在动画播放过程中，触发特定事件。