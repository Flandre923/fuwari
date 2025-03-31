---
title: Untity学习记录-实现一个完整的2D游戏Demo-25-场景管理和切换
published: 2025-03-31
tags: ['Unity','游戏','demo','2d']
description: 使用Untiy时间一个2D冒险游戏demo
image: https://cdn.jsdelivr.net/gh/Flandre923/CDN@latest/img/20250331195820.png
category: Unity
draft: false
---
在游戏开发中，资源管理是一个至关重要的课题。今天我们将深入探讨Unity的Addressable系统，这是一个强大的资源管理工具，可以帮助我们实现更灵活、更高效的场景和资源加载方式。

## Addressable系统概述

Addressable（可寻址资源系统）是Unity官方提供的一套资源管理解决方案，它主要有以下优势：

1. **减少包体大小**：避免重复打包相同资源
2. **灵活加载**：支持按需加载和卸载资源
3. **热更新支持**：可实现资源远程更新
4. **简化依赖管理**：自动处理资源依赖关系

### 安装与基础配置

首先我们需要通过Package Manager安装Addressable包：

1. 打开Window \> Package Manager
2. 搜索"Addressables"并安装
3. 安装后通过Window \> Asset Management \> Addressables \> Groups打开管理面板

首次使用时需要创建Addressable设置：

```csharp
// 自动生成的AddressableAssetSettings ScriptableObject
// 位于Assets/AddressableAssetsData/AddressableAssetSettings.asset
```

## 场景的Addressable配置

将场景设置为Addressable资源：

1. 在Project窗口选择场景文件
2. 在Inspector窗口勾选"Addressable"选项
3. 场景会自动添加到Default组（可重命名或创建新组）

```csharp
// 简化资源命名（右键菜单选择Simplify Addressable Names）
// 这样可以只使用场景名而非完整路径
```

## 预制体(PreFab)的Addressable管理

对于需要跨场景复用的敌人预制体：

1. 创建Prefabs文件夹存放预制体
2. 将敌人（野猪、蜗牛、蜜蜂）拖入成为预制体
3. 选中预制体并勾选"Addressable"
4. 创建专门的"Enemies"组进行分类管理

```csharp
// 预制体Addressable化的好处：
// 1. 避免重复打包
// 2. 实现按需加载
// 3. 支持热更新替换
```

## 场景加载管理器实现

我们创建一个SceneLoader脚本来管理场景加载：

```csharp
using UnityEngine;
using UnityEngine.AddressableAssets;
using UnityEngine.ResourceManagement.AsyncOperations;
using UnityEngine.SceneManagement;

public class SceneLoader : MonoBehaviour
{
    [SerializeField] private GameSceneSO firstLoadScene;
    private GameSceneSO currentLoadedScene;
  
    private void Awake()
    {
        // 初始加载第一个场景
        LoadScene(firstLoadScene);
    }
  
    public void LoadScene(GameSceneSO sceneToLoad)
    {
        StartCoroutine(UnloadPreviousSceneAndLoadNew(sceneToLoad));
    }
  
    private IEnumerator UnloadPreviousSceneAndLoadNew(GameSceneSO sceneToLoad)
    {
        if(currentLoadedScene != null)
        {
            // 异步卸载当前场景
            var unloadOp = Addressables.UnloadSceneAsync(
                currentLoadedScene.sceneReference);
            yield return unloadOp;
        }
    
        // 异步加载新场景
        var loadOp = Addressables.LoadSceneAsync(
            sceneToLoad.sceneReference, 
            LoadSceneMode.Additive);
        yield return loadOp;
    
        currentLoadedScene = sceneToLoad;
    }
}
```

### 场景数据ScriptableObject

创建GameSceneSO来存储场景信息：

```csharp
using UnityEngine;
using UnityEngine.AddressableAssets;

[CreateAssetMenu(menuName = "Game/Scene")]
public class GameSceneSO : ScriptableObject
{
    public AssetReference sceneReference;
    public SceneType sceneType; // Location/Menu等
  
    public enum SceneType
    {
        Location,
        Menu
        // 可扩展其他类型
    }
}
```

## 传送系统集成

实现传送点触发场景切换：

```csharp
public class TeleportPoint : MonoBehaviour, IInteractable
{
    [SerializeField] private GameSceneSO sceneToGo;
    [SerializeField] private Vector3 positionToGo;
  
    public void TriggerAction()
    {
        // 触发场景加载事件
        SceneLoader.Instance.LoadScene(sceneToGo, positionToGo);
    }
}
```

## 高级技巧与最佳实践

1. **异步操作处理**：使用AsyncOperationHandle跟踪加载状态
2. **依赖管理**：Addressable自动处理资源依赖
3. **内存管理**：及时卸载不再使用的资源
4. **错误处理**：添加加载失败的回调处理
5. **进度反馈**：显示加载进度条

```csharp
// 带进度反馈的加载示例
private IEnumerator LoadWithProgress(AssetReference sceneRef)
{
    var handle = Addressables.LoadSceneAsync(sceneRef);
  
    while(!handle.IsDone)
    {
        float progress = handle.PercentComplete;
        // 更新UI进度条
        yield return null;
    }
  
    if(handle.Status == AsyncOperationStatus.Succeeded)
    {
        // 加载成功处理
    }
    else
    {
        // 加载失败处理
    }
}
```

## 性能优化建议

1. **分组策略**：按使用频率和场景分组资源
2. **加载模式**：根据需求选择同步/异步加载
3. **预加载**：提前加载可能需要的资源
4. **引用计数**：管理资源引用避免过早释放
5. **分析工具**：使用Addressables Analyze工具优化

## 常见问题解决方案

1. **引用丢失**：确保所有Addressable引用正确设置
2. **加载卡顿**：使用异步加载和加载界面过渡
3. **内存泄漏**：正确释放不再使用的资源
4. **构建错误**：检查资源依赖和分组设置
5. **平台差异**：注意不同平台的资源处理方式