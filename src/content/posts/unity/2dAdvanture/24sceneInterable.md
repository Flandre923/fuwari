---
title: Untity学习记录-实现一个完整的2D游戏Demo-24-场景互动的逻辑和实现
published: 2025-03-31
tags: ['Unity','游戏','demo','2d']
description: 使用Untiy时间一个2D冒险游戏demo
image: https://cdn.jsdelivr.net/gh/Flandre923/CDN@latest/img/20250331195820.png
category: Unity
draft: false
---
欢迎回到我们的游戏开发系列！在之前的开发中，我们已经实现了角色动画和基础互动检测，今天我们将深入探讨如何通过接口(Interface)来实现游戏中的互动系统，包括宝箱开启和传送门等功能。

## 接口(Interface)基础

接口是面向对象编程中一个强大的概念，它定义了一组方法签名而不提供具体实现。任何实现了该接口的类都必须提供这些方法的具体实现。

```csharp
public interface IInteractable
{
    void TriggerAction();
}
```

接口的命名通常以"I"开头（Interface的缩写），它类似于抽象类但不包含任何实现代码。接口的主要优势在于：

1. **统一性**：不同类可以实现相同接口，保证它们都有相同的方法
2. **解耦**：代码不依赖于具体类，而是依赖于接口
3. **多态性**：可以通过接口类型来引用任何实现该接口的对象

## 实现宝箱互动系统

让我们首先实现一个简单的宝箱互动系统：

```csharp
public class Chest : MonoBehaviour, IInteractable
{
    [SerializeField] private Sprite closeSprite;
    [SerializeField] private Sprite openSprite;
  
    private SpriteRenderer spriteRenderer;
    private bool isDone;
  
    private void OnEnable()
    {
        spriteRenderer = GetComponent<SpriteRenderer>();
        spriteRenderer.sprite = isDone ? openSprite : closeSprite;
    }
  
    public void TriggerAction()
    {
        if(!isDone)
        {
            OpenChest();
        }
    }
  
    private void OpenChest()
    {
        spriteRenderer.sprite = openSprite;
        isDone = true;
        gameObject.tag = "Untagged"; // 互动后移除互动标签
    }
}
```

### 关键点解析：

1. **状态管理**：使用`isDone`布尔值来跟踪宝箱是否已被打开
2. **精灵切换**：通过`SpriteRenderer`组件切换开/关状态的图片
3. **标签管理**：互动后移除互动标签，防止重复互动

## 输入系统集成

我们需要设置输入系统来触发互动：

```csharp
// 在PlayerInputControl中设置
private void OnEnable()
{
    playerInput.Gameplay.Confirm.started += OnConfirmed;
}

private void OnConfirmed(InputAction.CallbackContext context)
{
    if(canPress)
    {
        targetItem?.TriggerAction();
    }
}

private void OnTriggerStay2D(Collider2D other)
{
    if(other.CompareTag("Interactable"))
    {
        targetItem = other.GetComponent<IInteractable>();
    }
}
```

### 输入系统要点：

1. **多设备支持**：同时监听键盘(E键)和手柄(东侧按钮)
2. **条件检测**：仅在`canPress`为true时触发互动
3. **接口调用**：通过接口统一调用不同互动对象的`TriggerAction`

## 音效集成

为互动添加音效增强游戏体验：

```csharp
[RequireComponent(typeof(AudioDefinition))]
public class Sign : MonoBehaviour
{
    private AudioDefinition audioDefinition;
  
    private void Awake()
    {
        audioDefinition = GetComponent<AudioDefinition>();
    }
  
    private void OnConfirmed(InputAction.CallbackContext context)
    {
        if(canPress)
        {
            targetItem?.TriggerAction();
            audioDefinition?.PlayAudioClip();
        }
    }
}
```

## 传送门系统实现

传送门是另一种互动对象，同样实现`IInteractable`接口：

```csharp
public class TeleportPoint : MonoBehaviour, IInteractable
{
    public Vector3 positionToGo;
  
    public void TriggerAction()
    {
        Debug.Log("准备传送...");
        // 实际传送逻辑将在场景管理器中实现
    }
}
```

### 传送系统设计考虑：

1. **目标位置**：存储传送后的目标坐标
2. **场景切换**：需要与场景管理器配合（下个视频实现）
3. **数据持久化**：需要跨场景保存传送信息

## 接口的优势总结

通过接口实现互动系统带来了以下好处：

1. **代码复用**：所有互动对象共享相同接口
2. **扩展性**：添加新互动类型只需实现接口，无需修改现有代码
3. **类型安全**：编译时检查确保所有互动对象都有必要方法
4. **多态性**：可以统一处理不同类型的互动对象