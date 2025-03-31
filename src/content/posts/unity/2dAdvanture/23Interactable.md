---
title: Untity学习记录-实现一个完整的2D游戏Demo-23-人物互动标识
published: 2025-03-31
tags: ['Unity','游戏','demo','2d']
description: 使用Untiy时间一个2D冒险游戏demo
image: https://cdn.jsdelivr.net/gh/Flandre923/CDN@latest/img/20250331195820.png
category: Unity
draft: false
---
大家好！今天我们来探讨游戏中的交互系统实现，重点是如何设计通用的交互机制，并支持多输入设备的动态切换提示。这个系统将用于场景传送、宝箱开启等各类游戏互动。

## 交互系统架构设计

### 核心组件结构

```csharp
交互系统主要包含三部分：
1. **交互标识(Sign)**：
   - 显示在玩家头顶的提示UI
   - 动态适配不同输入设备
   - 碰撞检测触发显示/隐藏

2. **可交互物体(Interactable)**：
   - 实现特定交互逻辑
   - 标记为"Interactable"标签
   - 挂载具体交互脚本

3. **输入管理系统**：
   - 检测当前输入设备类型
   - 统一管理交互按键
   - 处理设备切换事件
```

## 交互标识实现

### 动画控制器设置

```csharp
// SignAnimatorController 配置要点：
- 创建空默认状态(Default)
- 添加不同设备对应的动画状态
- 不设置过渡条件，通过代码直接播放
- 动画状态命名与设备类型匹配（如"Keyboard"、"Gamepad"）
```

### 标识控制脚本

```csharp
// SignController.cs
using UnityEngine;
using UnityEngine.InputSystem;

public class SignController : MonoBehaviour
{
    [SerializeField] private SpriteRenderer spriteRenderer;
    [SerializeField] private Animator animator;
    [SerializeField] private Transform playerTransform;
  
    private bool canInteract;
    private InputAction interactAction;

    private void Awake()
    {
        // 初始化输入
        var playerInput = new PlayerInputControl();
        interactAction = playerInput.Player.Interact;
        playerInput.Enable();
    
        // 监听设备变化
        InputSystem.onActionChange += OnInputDeviceChanged;
    }

    private void OnDestroy()
    {
        InputSystem.onActionChange -= OnInputDeviceChanged;
    }

    private void Update()
    {
        // 同步玩家朝向
        transform.localScale = playerTransform.localScale;
    
        // 控制显示/隐藏
        spriteRenderer.enabled = canInteract;
    
        // 交互检测
        if (canInteract && interactAction.triggered)
        {
            // 触发交互逻辑...
        }
    }

    private void OnTriggerEnter2D(Collider2D other)
    {
        if (other.CompareTag("Interactable"))
        {
            canInteract = true;
            UpdateButtonPrompt(other.GetComponent<InputDeviceType>());
        }
    }

    private void OnTriggerExit2D(Collider2D other)
    {
        if (other.CompareTag("Interactable"))
        {
            canInteract = false;
        }
    }

    private void OnInputDeviceChanged(object obj, InputActionChange change)
    {
        if (change == InputActionChange.ActionStarted)
        {
            var device = ((InputAction)obj).activeControl.device;
            UpdateButtonPrompt(GetDeviceType(device));
        }
    }

    private void UpdateButtonPrompt(InputDeviceType deviceType)
    {
        animator.Play(deviceType.ToString());
    }
  
    private InputDeviceType GetDeviceType(InputDevice device)
    {
        // 设备类型判断逻辑...
    }
}

public enum InputDeviceType { Keyboard, Gamepad }
```

## 多设备输入适配

### 设备类型检测

```csharp
// 扩展设备类型判断
private InputDeviceType GetDeviceType(InputDevice device)
{
    switch (device)
    {
        case Keyboard:
            return InputDeviceType.Keyboard;
        case Gamepad:
            return InputDeviceType.Gamepad;
        default:
            return InputDeviceType.Keyboard; // 默认键盘
    }
}
```

### 输入动作配置

```csharp
// Input Action Asset配置要点：
1. 创建"Interact"动作
2. 添加多设备绑定：
   - 键盘：E键
   - 游戏手柄：South按钮(PS○/XboxB)
3. 交互类型设置为"Button"
```

## 可交互接口设计

### 交互接口定义

```csharp
// IInteractable.cs
public interface IInteractable
{
    InputDeviceType PreferredDevice { get; }
    void Interact();
}
```

### 宝箱交互实现

```csharp
// ChestInteractable.cs
public class ChestInteractable : MonoBehaviour, IInteractable
{
    [SerializeField] private Sprite openSprite;
    [SerializeField] private AudioClip openSound;
  
    public InputDeviceType PreferredDevice => InputDeviceType.Keyboard;
  
    private SpriteRenderer spriteRenderer;
    private bool isOpen;

    private void Awake()
    {
        spriteRenderer = GetComponent<SpriteRenderer>();
    }

    public void Interact()
    {
        if (isOpen) return;
    
        spriteRenderer.sprite = openSprite;
        AudioManager.Instance.PlaySFX(openSound);
        isOpen = true;
    
        // 触发宝箱奖励逻辑...
    }
}
```

## 场景传送门实现

### 基础传送组件

```csharp
// DoorInteractable.cs
public class DoorInteractable : MonoBehaviour, IInteractable
{
    [SerializeField] private string targetScene;
    [SerializeField] private Vector2 spawnPosition;
  
    public InputDeviceType PreferredDevice => InputDeviceType.Gamepad;

    public void Interact()
    {
        SceneLoader.Instance.LoadScene(targetScene, spawnPosition);
    }
}
```

## 系统集成与优化

### 交互管理器

```csharp
// InteractionManager.cs
public class InteractionManager : MonoBehaviour
{
    public static InteractionManager Instance { get; private set; }
  
    private List<IInteractable> interactables = new List<IInteractable>();
  
    private void Awake()
    {
        if (Instance == null)
        {
            Instance = this;
            DontDestroyOnLoad(gameObject);
        }
        else
        {
            Destroy(gameObject);
        }
    }
  
    public void Register(IInteractable interactable)
    {
        if (!interactables.Contains(interactable))
            interactables.Add(interactable);
    }
  
    public void Unregister(IInteractable interactable)
    {
        interactables.Remove(interactable);
    }
}
```

### 性能优化建议

1. **对象池管理**：频繁交互的物体使用对象池
2. **输入优化**：使用Input System的Action回调替代每帧检测
3. **事件系统**：交互事件使用ScriptableObject事件通道
4. **分层渲染**：交互提示UI使用单独渲染层

## 调试与测试工具

### 编辑器扩展

```csharp
#if UNITY_EDITOR
[CustomEditor(typeof(InteractableBase))]
public class InteractableEditor : Editor
{
    public override void OnInspectorGUI()
    {
        base.OnInspectorGUI();
    
        if (GUILayout.Button("Test Interact"))
        {
            ((IInteractable)target).Interact();
        }
    }
}
#endif
```

### 调试可视化

```csharp
void OnDrawGizmos()
{
    Gizmos.color = Color.green;
    Gizmos.DrawWireCube(transform.position, interactionSize);
  
    if (canInteract)
    {
        Gizmos.color = Color.yellow;
        Gizmos.DrawSphere(transform.position + Vector3.up * 2f, 0.5f);
    }
}
```

## 扩展功能思路

1. **上下文交互**：

    * 根据场景显示不同交互提示
    * 动态交互内容（如对话树）
2. **辅助功能**：

    * 交互按键重绑定
    * 视觉障碍模式（增强提示）
3. **高级反馈**：

    * 交互物体高亮
    * 触觉反馈（手柄震动）

```csharp
// 手柄震动反馈示例
private IEnumerator RumbleController(float lowFreq, float highFreq, float duration)
{
    if (Gamepad.current != null)
    {
        Gamepad.current.SetMotorSpeeds(lowFreq, highFreq);
        yield return new WaitForSeconds(duration);
        InputSystem.ResetHaptics();
    }
}

```