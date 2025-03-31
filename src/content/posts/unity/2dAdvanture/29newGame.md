---
title: Untity学习记录-实现一个完整的2D游戏Demo-29-实现新的冒险的逻辑
published: 2025-03-31
tags: ['Unity','游戏','demo','2d']
description: 使用Untiy时间一个2D冒险游戏demo
image: https://cdn.jsdelivr.net/gh/Flandre923/CDN@latest/img/20250331195820.png
category: Unity
draft: false
---
在游戏开发中，完整的游戏流程控制是提升玩家体验的关键。今天我们将深入探讨如何实现从主菜单到游戏存档的全套系统，包括新游戏开始、游戏退出和存档点设计。

## 场景管理系统重构

### 变量命名优化

```csharp
// 旧变量命名
public GameSceneSO firstLoadScene;
public Vector3 firstPosition;

// 新变量命名
public GameSceneSO menuScene;
public Vector3 menuPosition;
public GameSceneSO firstGameScene; 
public Vector3 firstGamePosition;

```

### 启动流程调整

```csharp
private void Start()
{
    // 初始加载菜单场景
    LoadScene(menuScene, menuPosition);
}

public void StartNewGame()
{
    // 加载第一个游戏场景
    LoadScene(firstGameScene, firstGamePosition);
  
    // 重置玩家状态
    ResetPlayerState();
}
```

## 主菜单系统实现

### 按钮导航系统

```csharp
public class MenuController : MonoBehaviour
{
    [SerializeField] private Button defaultSelectedButton;
  
    private void OnEnable()
    {
        // 设置默认选中按钮
        EventSystem.current.SetSelectedGameObject(defaultSelectedButton.gameObject);
    }
  
    // 配置按钮导航
    private void SetupButtonNavigation()
    {
        Button[] buttons = GetComponentsInChildren<Button>();
        for(int i = 0; i < buttons.Length; i++)
        {
            Navigation nav = new Navigation()
            {
                mode = Navigation.Mode.Explicit,
                selectOnUp = i > 0 ? buttons[i-1] : buttons[buttons.Length-1],
                selectOnDown = i < buttons.Length-1 ? buttons[i+1] : buttons[0]
            };
            buttons[i].navigation = nav;
        }
    }
}
```

### 退出游戏实现

```csharp
public void QuitGame()
{
    #if UNITY_EDITOR
        Debug.Log("游戏退出");
        UnityEditor.EditorApplication.isPlaying = false;
    #else
        Application.Quit();
    #endif
}
```

## 游戏状态重置系统

### 角色状态管理

```csharp
public class Character : MonoBehaviour
{
    [SerializeField] private FloatValueSO healthSO;
    [SerializeField] private FloatValueSO powerSO;
  
    private void OnNewGame()
    {
        // 重置血量和能量
        healthSO.Value = healthSO.maxValue;
        powerSO.Value = powerSO.maxValue;
    
        // 更新UI
        UpdateHealthUI();
    }
  
    private void OnEnable()
    {
        // 注册新游戏事件
        GameEvents.OnNewGame += OnNewGame;
    }
  
    private void OnDisable()
    {
        // 注销事件
        GameEvents.OnNewGame -= OnNewGame;
    }
}

```

### 敌人状态重置

```csharp
public class Enemy : MonoBehaviour
{
    private void OnNewGame()
    {
        // 重置敌人状态
        ResetEnemy();
    }
  
    private void ResetEnemy()
    {
        // 重置血量、位置等状态
        currentHealth = maxHealth;
        transform.position = spawnPosition;
        gameObject.SetActive(true);
    }
}
```

## UI状态同步系统

### 场景过渡UI控制

```csharp
public class UIManager : MonoBehaviour
{
    [SerializeField] private GameObject playerHUD;
  
    private void OnSceneUnloaded(GameSceneSO unloadedScene)
    {
        // 场景卸载时隐藏HUD
        if(unloadedScene.sceneType == SceneType.Location)
        {
            playerHUD.SetActive(false);
        }
    }
  
    private void OnSceneLoaded(GameSceneSO loadedScene)
    {
        // 游戏场景加载时显示HUD
        if(loadedScene.sceneType == SceneType.Location)
        {
            playerHUD.SetActive(true);
        }
    }
}
```

## 存档系统基础设计

### 存档数据结构

```csharp
[System.Serializable]
public class SaveData
{
    public string sceneName;
    public Vector3 playerPosition;
    public float playerHealth;
    public float playerPower;
    public List<EnemyData> enemyStates;
    public List<ChestData> chestStates;
  
    [System.Serializable]
    public class EnemyData
    {
        public string enemyId;
        public Vector3 position;
        public float health;
        public bool isActive;
    }
  
    [System.Serializable]
    public class ChestData
    {
        public string chestId;
        public bool isOpened;
    }
}
```

### 存档点实现

```csharp
public class SavePoint : MonoBehaviour, IInteractable
{
    [SerializeField] private GameObject saveEffect;
  
    public void TriggerAction()
    {
        // 播放存档特效
        Instantiate(saveEffect, transform.position, Quaternion.identity);
    
        // 执行存档
        SaveSystem.SaveGame();
    
        // 显示存档提示
        UIManager.Instance.ShowSaveNotification();
    }
}
```

## 游戏事件系统

### 全局事件定义

```csharp
public static class GameEvents
{
    // 新游戏事件
    public static event Action OnNewGame;
    public static void RaiseNewGame() => OnNewGame?.Invoke();
  
    // 游戏保存事件
    public static event Action OnGameSaved;
    public static void RaiseGameSaved() => OnGameSaved?.Invoke();
  
    // 游戏加载事件
    public static event Action OnGameLoaded;
    public static void RaiseGameLoaded() => OnGameLoaded?.Invoke();
}
```

## 性能优化建议

1. **事件管理**：确保所有事件在适当时机注销
2. **对象池应用**：复用存档特效等临时对象
3. **异步存档**：避免存档操作阻塞主线程
4. **内存优化**：合理设计存档数据结构大小
5. **错误处理**：添加存档失败的回调处理

## 多平台适配

### 存档路径处理

```csharp
public static class SaveSystem
{
    public static string GetSavePath(string saveFileName)
    {
        #if UNITY_STANDALONE
            return Path.Combine(Application.dataPath, "Saves", saveFileName);
        #elif UNITY_ANDROID || UNITY_IOS
            return Path.Combine(Application.persistentDataPath, saveFileName);
        #else
            return Path.Combine(Application.dataPath, saveFileName);
        #endif
    }
}
```

通过这套系统，我们实现了：

* 完整的菜单导航系统
* 游戏状态重置机制
* 场景过渡UI同步
* 存档系统基础架构
* 跨平台兼容设计