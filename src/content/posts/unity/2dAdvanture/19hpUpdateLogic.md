---
title: Untity学习记录-实现一个完整的2D游戏Demo-19-血量更新逻辑的实现
published: 2025-03-31
tags: ['Unity','游戏','demo','2d']
description: 使用Untiy时间一个2D冒险游戏demo
image: https://cdn.jsdelivr.net/gh/Flandre923/CDN@latest/img/20250331195820.png
category: Unity
draft: false
---
利用ScriptableObject构建灵活的事件系统，并实现动态变化的UI元素。这个系统不仅能让你的代码更加模块化，还能实现跨场景的数据传递和UI更新。

### 基础概念

ScriptableObject是Unity中一种特殊的可序列化对象，它有以下特点：

* 作为资产文件(.asset)存储在项目中
* 不依赖于场景，可跨场景使用
* 适合存储全局数据和事件系统

### 创建事件通道

```csharp
// CharacterEventSO.cs
using UnityEngine;
using UnityEngine.Events;

[CreateAssetMenu(menuName = "Events/Character Event")]
public class CharacterEventSO : ScriptableObject
{
    public UnityAction<Character> OnEventRaised;
  
    public void RaiseEvent(Character character)
    {
        OnEventRaised?.Invoke(character);
    }
}
```

**关键点解析**：

1. `[CreateAssetMenu]`：在Unity创建菜单中添加选项
2. `UnityAction<T>`：带参数的委托类型
3. `?.Invoke()`：安全调用模式

## UI动态更新实现

### 血条组件控制

```csharp
// PlayerStatusBar.cs
using UnityEngine;
using UnityEngine.UI;

public class PlayerStatusBar : MonoBehaviour
{
    [Header("Health References")]
    [SerializeField] private Image healthImage;
    [SerializeField] private Image healthDelayImage;
  
    [Header("Settings")]
    [SerializeField] private float delaySpeed = 0.5f;
  
    private void Update()
    {
        // 延迟血条效果
        if (healthDelayImage.fillAmount > healthImage.fillAmount)
        {
            healthDelayImage.fillAmount -= Time.deltaTime * delaySpeed;
        }
    }
  
    public void OnHealthChange(float percentage)
    {
        healthImage.fillAmount = percentage;
    }
}
```

### 事件订阅与取消

```csharp
// UIManager.cs
using UnityEngine;

public class UIManager : MonoBehaviour
{
    [Header("Event Listening")]
    [SerializeField] private CharacterEventSO healthEvent;
  
    [Header("UI References")]
    [SerializeField] private PlayerStatusBar playerStatusBar;
  
    private void OnEnable()
    {
        healthEvent.OnEventRaised += OnHealthEvent;
    }
  
    private void OnDisable()
    {
        healthEvent.OnEventRaised -= OnHealthEvent;
    }
  
    private void OnHealthEvent(Character character)
    {
        float percentage = (float)character.CurrentHealth / character.MaxHealth;
        playerStatusBar.OnHealthChange(percentage);
    }
}
```

## 系统整合流程

1. **数据发送端(Character)** ：

```csharp
// 血量变化时触发事件
public void TakeDamage(int damage)
{
    CurrentHealth -= damage;
    healthEvent.RaiseEvent(this);
}
```

1. **事件通道(CharacterEventSO)** ：

    * 作为ScriptableObject资产存在
    * 负责传递Character数据
2. **数据接收端(UIManager)** ：

    * 订阅事件并更新UI
    * 实现延迟血条效果

## 设计优势分析

1. **解耦设计**：

    * Character不需要知道谁在监听血量变化
    * UI元素可以自由添加/移除而不影响游戏逻辑
2. **跨场景通信**：

    * ScriptableObject不受场景加载影响
    * 适合全局状态管理
3. **可视化调试**：

    * 可以在Inspector中查看事件订阅情况
    * 方便调整血条延迟速度等参数

## 性能优化建议

1. **事件管理**：

    * 及时取消不需要的事件订阅
    * 避免频繁触发事件（如每帧更新）
2. **UI更新**：

    * 使用CanvasGroup控制UI元素的显示/隐藏
    * 对静态UI元素启用Raycast Target优化
3. **资源管理**：

    * 使用对象池管理频繁变化的UI元素
    * 对血条等动态元素使用GPU Instancing

## 扩展应用场景

1. **成就系统**：

```csharp
// 成就事件示例
public class AchievementEventSO : ScriptableObject
{
    public UnityAction<string> OnAchievementUnlocked;
}
```

2. **任务系统**：

```csharp
// 任务进度更新
public class QuestEventSO : ScriptableObject
{
    public UnityAction<Quest> OnQuestProgressUpdated;
}
```

3. **全局设置**：

```csharp
// 游戏设置更改
public class SettingsEventSO : ScriptableObject
{
    public UnityAction<GameSettings> OnSettingsChanged;
}
```

## 调试技巧

1. **事件日志**：

```csharp
private void OnHealthEvent(Character character)
{
    Debug.Log($"Health changed: {character.CurrentHealth}/{character.MaxHealth}");
    // ...其他逻辑
}
```

2. **可视化调试工具**：

* 使用Unity的Frame Debugger分析UI渲染
* 通过Profiler监控事件系统的性能影响

3. **编辑器扩展**：

```csharp
#if UNITY_EDITOR
[CustomEditor(typeof(CharacterEventSO))]
public class CharacterEventSOEditor : Editor
{
    // 自定义Inspector界面
}
#endif
```