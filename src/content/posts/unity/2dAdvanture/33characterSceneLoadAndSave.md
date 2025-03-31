---
title: Untity学习记录-实现一个完整的2D游戏Demo-33-制作结束面板
published: 2025-03-31
tags: ['Unity','游戏','demo','2d']
description: 使用Untiy时间一个2D冒险游戏demo
image: https://cdn.jsdelivr.net/gh/Flandre923/CDN@latest/img/20250331195820.png
category: Unity
draft: false
---
大家好！今天我要分享如何在Unity中实现一个完整的游戏死亡与重生系统，包括死亡面板、重生逻辑和返回主菜单功能。这个系统将让你的游戏体验更加完整和专业。

## 1. 死亡面板UI设计

### 1.1 创建基础面板

首先创建一个美观的死亡面板UI：

```csharp
// 创建死亡面板的步骤：
1. 在Main Canvas下创建新Panel，命名为"GameOverPanel"
2. 移除默认Image组件，添加Canvas Group组件方便控制透明度
3. 设置背景色为黑色，透明度约50%（Alpha值130）
4. 添加标题文本"Game Over"（使用TextMeshPro）
5. 添加两个按钮："重新开始"和"返回主菜单"
```

### 1.2 按钮样式设置

```csharp
// 按钮组件配置示例：
[SerializeField] private TextMeshProUGUI restartText;
[SerializeField] private TextMeshProUGUI menuText;
[SerializeField] private Button restartButton;
[SerializeField] private Button menuButton;

private void SetupButtons()
{
    // 重新开始按钮
    restartButton.onClick.AddListener(OnRestartClicked);
    var restartColors = restartButton.colors;
    restartColors.normalColor = new Color(0.2f, 0.8f, 0.2f); // 绿色
    restartColors.highlightedColor = new Color(0.3f, 0.9f, 0.3f);
    restartButton.colors = restartColors;
  
    // 返回菜单按钮
    menuButton.onClick.AddListener(OnMenuClicked);
    var menuColors = menuButton.colors;
    menuColors.normalColor = new Color(0.2f, 0.2f, 0.8f); // 蓝色
    menuColors.highlightedColor = new Color(0.3f, 0.3f, 0.9f);
    menuButton.colors = menuColors;
}
```

## 2. 死亡逻辑实现

### 2.1 角色死亡状态管理

在Character类中实现死亡逻辑：

```csharp
public class Character : MonoBehaviour
{
    [SerializeField] private UnityEvent onDieEvent;
    private bool isDead = false;
  
    public void TakeDamage(float damage)
    {
        if(isDead) return;
    
        currentHealth -= damage;
    
        if(currentHealth <= 0)
        {
            Die();
        }
    }
  
    private void Die()
    {
        isDead = true;
        animator.SetBool("IsDead", true);
        onDieEvent?.Invoke(); // 触发死亡事件
    
        // 禁用玩家输入
        if(TryGetComponent<PlayerController>(out var controller))
        {
            controller.SetInputEnabled(false);
        }
    }
  
    public void Revive()
    {
        isDead = false;
        animator.SetBool("IsDead", false);
        currentHealth = maxHealth;
    
        if(TryGetComponent<PlayerController>(out var controller))
        {
            controller.SetInputEnabled(true);
        }
    }
}
```

### 2.2 死亡面板触发

```csharp
public class UIManager : MonoBehaviour
{
    [SerializeField] private GameObject gameOverPanel;
    [SerializeField] private GameObject firstSelectedOnDeath;
  
    private void OnEnable()
    {
        EventManager.Instance.AddListener<PlayerDeathEvent>(OnPlayerDeath);
    }
  
    private void OnDisable()
    {
        EventManager.Instance?.RemoveListener<PlayerDeathEvent>(OnPlayerDeath);
    }
  
    private void OnPlayerDeath(PlayerDeathEvent evt)
    {
        gameOverPanel.SetActive(true);
    
        // 设置默认选中按钮
        EventSystem.current.SetSelectedGameObject(firstSelectedOnDeath);
    }
}
```

## 3. 重生系统实现

### 3.1 从存档点重生

```csharp
public class GameManager : MonoBehaviour
{
    public void RestartFromLastSave()
    {
        // 1. 加载存档数据
        SaveSystem.Instance.LoadGame();
    
        // 2. 复活玩家
        var player = FindObjectOfType<PlayerController>();
        if(player != null)
        {
            player.Revive();
        }
    
        // 3. 关闭死亡面板
        UIManager.Instance.HideGameOverPanel();
    }
  
    public void ReturnToMainMenu()
    {
        // 1. 加载主菜单场景
        SceneManager.LoadScene("MainMenu");
    
        // 2. 复活玩家（防止菜单中角色保持死亡状态）
        var player = FindObjectOfType<PlayerController>();
        if(player != null)
        {
            player.Revive();
        }
    
        // 3. 关闭死亡面板
        UIManager.Instance.HideGameOverPanel();
    }
}
```

### 3.2 输入控制管理

```csharp
public class PlayerController : MonoBehaviour
{
    private PlayerInputActions inputActions;
    private bool inputEnabled = true;
  
    private void Awake()
    {
        inputActions = new PlayerInputActions();
        inputActions.Player.Enable();
    }
  
    public void SetInputEnabled(bool enabled)
    {
        inputEnabled = enabled;
        if(enabled)
        {
            inputActions.Player.Enable();
        }
        else
        {
            inputActions.Player.Disable();
        }
    }
  
    private void Update()
    {
        if(!inputEnabled) return;
    
        // 正常输入处理...
    }
}
```

## 4. 事件系统优化

使用UnityEvent构建松耦合系统：

```csharp
// 自定义事件类
public class PlayerDeathEvent : GameEvent { }
public class PlayerReviveEvent : GameEvent { }

// 事件管理器
public class EventManager : Singleton<EventManager>
{
    private Dictionary<Type, GameEvent> eventDictionary = new();
  
    public void AddListener<T>(Action<T> handler) where T : GameEvent
    {
        // 实现添加监听器逻辑
    }
  
    public void Raise<T>(T gameEvent) where T : GameEvent
    {
        // 实现触发事件逻辑
    }
}

// 在角色死亡时触发事件
private void Die()
{
    isDead = true;
    EventManager.Instance.Raise(new PlayerDeathEvent());
}
```
## 7. 总结与最佳实践

通过本教程，我们实现了：

1. **完整的死亡流程**：从角色死亡到UI反馈
2. **灵活的重生系统**：支持从存档点重生或返回主菜单
3. **健壮的输入管理**：确保UI和游戏状态的正确切换
4. **事件驱动架构**：保持代码松耦合和可扩展性