---
title: Untity学习记录-实现一个完整的2D游戏Demo-20-摄像机跟随和攻击抖动实现
published: 2025-03-31
tags: ['Unity','游戏','demo','2d']
description: 使用Untiy时间一个2D冒险游戏demo
image: https://cdn.jsdelivr.net/gh/Flandre923/CDN@latest/img/20250331195820.png
category: Unity
draft: false
---
大家好！今天我们来深入探讨Unity中强大的Cinemachine摄像机系统，以及如何用它来打造专业级的2D游戏摄像机效果。从基础跟随到高级震动反馈，让我们一步步构建完美的游戏视角系统。

## Cinemachine基础配置

### 安装与设置

1. **安装步骤**：

    * 通过Package Manager安装Cinemachine（当前最新2.9.5+）
    * 创建2D Virtual Camera：`Cinemachine → Create 2D Camera`
2. **基础跟随**：

```csharp
// 配置Follow和Look At目标
VirtualCamera.Follow = playerTransform;
VirtualCamera.LookAt = playerTransform;
```

### 关键参数解析

| 参数      | 作用                         | 推荐值          |
| ----------- | ------------------------------ | ----------------- |
| Dead Zone | 玩家移动触发摄像机跟随的阈值 | 宽度0.3,高度0.2 |
| Soft Zone | 摄像机平滑跟随的区域         | 宽度0.8,高度0.6 |
| Lookahead | 预测玩家移动方向             | 开启，Time 0.5  |

```csharp
// 调整摄像机跟随偏移
var transposer = VirtualCamera.GetCinemachineComponent<CinemachineFramingTransposer>();
transposer.m_ScreenY = 0.65f; // 提高Y轴视角
```

## 摄像机边界限制

### 多边形碰撞体边界

1. **创建步骤**：

    * 新建空物体命名为"Bounds"
    * 添加`PolygonCollider2D`组件并勾选Is Trigger
    * 编辑碰撞体形状匹配场景边界
2. **代码动态绑定**：

```csharp
// CameraController.cs
using Cinemachine;

public class CameraController : MonoBehaviour
{
    private CinemachineConfiner2D confiner;
  
    void Awake()
    {
        confiner = GetComponent<CinemachineConfiner2D>();
        UpdateCameraBounds();
    }
  
    public void UpdateCameraBounds()
    {
        var boundsObj = GameObject.FindWithTag("Bounds");
        if (boundsObj != null)
        {
            confiner.m_BoundingShape2D = boundsObj.GetComponent<Collider2D>();
            confiner.InvalidateCache();
        }
    }
}
```

多场景边界切换

```csharp
// 场景加载后调用
SceneManager.sceneLoaded += (scene, mode) => {
    FindObjectOfType<CameraController>().UpdateCameraBounds();
};
```

## 摄像机震动系统

### 震动源配置

1. **创建Impulse Source**：

    * 新建空物体添加`CinemachineImpulseSource`组件
    * 预设选择"Bump"或自定义震动曲线
    * 关键参数：

      * Impulse Shape：震动波形
      * Velocity：各轴震动强度
      * Dissipation：震动衰减速度
2. **震动监听器**：

```csharp
// 在Virtual Camera上添加
CinemachineImpulseListener listener = vcam.AddExtension<CinemachineImpulseListener>();
listener.m_Use2DDistance = true; // 2D游戏必选
```

### 事件驱动震动

```csharp
// CameraShakeEventSO.cs
[CreateAssetMenu(menuName = "Events/Camera Shake Event")]
public class CameraShakeEventSO : ScriptableObject
{
    public UnityAction OnCameraShakeRequested;
  
    public void RaiseEvent()
    {
        OnCameraShakeRequested?.Invoke();
    }
}

// 在角色受伤时触发
public void TakeDamage(int damage)
{
    // ...伤害计算逻辑
    cameraShakeEvent.RaiseEvent();
}
```

## 高级技巧与优化

### 多摄像机混合

```csharp
// 实现过场动画切换
CinemachineBrain brain = Camera.main.GetComponent<CinemachineBrain>();
brain.m_DefaultBlend = new CinemachineBlendDefinition(
    CinemachineBlendDefinition.Style.EaseInOut, 
    1.5f); // 过渡时间
```