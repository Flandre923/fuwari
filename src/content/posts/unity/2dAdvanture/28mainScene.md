---
title: Untity学习记录-实现一个完整的2D游戏Demo-28-主场景制作
published: 2025-03-31
tags: ['Unity','游戏','demo','2d']
description: 使用Untiy时间一个2D冒险游戏demo
image: https://cdn.jsdelivr.net/gh/Flandre923/CDN@latest/img/20250331195820.png
category: Unity
draft: false
---
在游戏开发中，主菜单系统是玩家接触游戏的第一界面，它的实现需要考虑UI设计、场景管理和游戏流程控制等多个方面。今天我们将深入探讨如何构建一个专业级的游戏主菜单系统。

## 主菜单场景搭建

### 基础场景配置

```csharp
// 主菜单场景必备元素
1. 主摄像机（渲染Tilemap和UI）
2. 背景Tilemap（使用规则瓦片或图片）
3. UI Canvas（Sort Order设置为5，高于游戏内UI）
```

### 图片对齐技巧

使用Unity的顶点吸附功能快速对齐背景图片：

1. 按住V键激活顶点吸附模式
2. 拖动图片顶点到目标位置
3. 自动对齐相邻图片边缘

## TextMesh Pro高级应用

### 中文字体集成

```csharp
// 使用TextMesh Pro支持中文的步骤：
1. 导入中文字体文件（如得意黑）
2. 右键字体 > Create > TextMesh Pro > Font Asset
3. 调整Font Asset设置：
   - Atlas Resolution: 4096x4096（容纳更多汉字）
   - Character Set: 自定义字符集（按需添加）
```

### 文字效果实现

```csharp
// 创建渐变文字效果
1. 启用Color Gradient选项
2. 设置渐变方向（Horizontal/Vertical）
3. 调整顶部/底部颜色
4. 可选添加轮廓(Outline)效果
```

## 按钮系统实现

### 按钮状态配置

```csharp
// 按钮颜色状态管理
[Serializable]
public class ButtonColors
{
    public Color normalColor;
    public Color highlightedColor;
    public Color pressedColor;
    public Color selectedColor;
}

// 应用示例
[SerializeField] private ButtonColors startButtonColors;
button.colors = new ColorBlock()
{
    normalColor = startButtonColors.normalColor,
    highlightedColor = startButtonColors.highlightedColor,
    pressedColor = startButtonColors.pressedColor,
    selectedColor = startButtonColors.selectedColor,
    colorMultiplier = 1,
    fadeDuration = 0.1f
};

```

### 按钮布局优化

使用Vertical Layout Group实现自动排列：

```csharp
// 按钮组布局设置
verticalLayoutGroup.spacing = 20; // 按钮间距
verticalLayoutGroup.childAlignment = TextAnchor.MiddleCenter; // 居中对齐
verticalLayoutGroup.padding = new RectOffset(0, 0, 50, 50); // 内边距
```

## 场景管理系统增强

### 菜单场景特殊处理

```csharp
public class SceneLoader : MonoBehaviour
{
    private void HandleSceneLoad(GameSceneSO sceneToLoad, Vector3 pos, bool fadeScreen)
    {
        if(sceneToLoad.sceneType == SceneType.Menu)
        {
            // 禁用玩家控制
            playerController.DisableControl();
            // 隐藏状态UI
            uiManager.HidePlayerHUD();
        }
        else
        {
            // 启用玩家控制
            playerController.EnableControl();
            // 显示状态UI
            uiManager.ShowPlayerHUD();
        }
    }
}
```

### 新游戏启动流程

```csharp
public class MainMenu : MonoBehaviour
{
    [SerializeField] private GameSceneSO startingScene;
    [SerializeField] private Vector3 startingPosition;
  
    public void OnNewGameClicked()
    {
        // 触发场景加载事件
        SceneLoader.Instance.LoadScene(startingScene, startingPosition);
    
        // 播放过渡音效
        audioManager.Play("StartGame");
    
        // 可选：禁用菜单按钮防止重复点击
        SetInteractable(false);
    }
}
```

## 游戏流程控制

### 玩家输入管理

```csharp
public class PlayerController : MonoBehaviour
{
    private void OnEnable()
    {
        SceneLoader.OnSceneLoadStarted += DisableControl;
        SceneLoader.OnSceneLoadCompleted += EnableControlForGameplay;
    }
  
    private void EnableControlForGameplay(GameSceneSO loadedScene)
    {
        if(loadedScene.sceneType == SceneType.Location)
        {
            EnableControl();
        }
    }
}
```

### UI状态同步

```csharp
public class UIManager : MonoBehaviour
{
    private void HandleSceneLoaded(GameSceneSO loadedScene)
    {
        playerHUD.SetActive(loadedScene.sceneType == SceneType.Location);
        pauseMenu.SetAvailable(loadedScene.sceneType == SceneType.Location);
    }
}
```

## 高级功能实现

### 场景过渡动画序列

```csharp
public IEnumerator StartNewGameSequence()
{
    // 淡出菜单
    fadeCanvas.FadeOut(0.5f);
    yield return new WaitForSeconds(0.5f);
  
    // 加载游戏场景
    SceneLoader.Instance.LoadScene(startingScene, startingPosition);
  
    // 等待场景加载
    while(SceneLoader.Instance.IsLoading)
    {
        yield return null;
    }
  
    // 淡入游戏画面
    fadeCanvas.FadeIn(0.5f);
}
```

### 按钮音效反馈

```csharp
public class MenuButton : MonoBehaviour
{
    [SerializeField] private AudioClip hoverSound;
    [SerializeField] private AudioClip clickSound;
  
    private AudioSource audioSource;
  
    private void Awake()
    {
        audioSource = GetComponent<AudioSource>();
        var button = GetComponent<Button>();
    
        button.onClick.AddListener(() => audioSource.PlayOneShot(clickSound));
    }
  
    public void OnPointerEnter()
    {
        audioSource.PlayOneShot(hoverSound);
    }
}
```

## 性能优化建议

1. **字体内存管理**：仅加载必要的中文字符
2. **UI批处理**：保持菜单元素在相同Canvas下
3. **资源预加载**：提前加载游戏初始场景资源
4. **事件清理**：确保注销所有事件监听
5. **对象池应用**：复用菜单动画对象

## 多平台适配

### 移动设备优化

```csharp
// 调整按钮大小和间距以适应触摸屏
[Serializable]
public class MobileUIAdjustments
{
    public float buttonSpacing = 40f;
    public int fontSize = 48;
    public Vector2 buttonSize = new Vector2(400, 120);
}

#if UNITY_IOS || UNITY_ANDROID
    [SerializeField] private MobileUIAdjustments mobileSettings;
#endif
```

### 控制器导航

```csharp
// 设置按钮导航支持游戏手柄
void SetupButtonNavigation()
{
    var buttons = GetComponentsInChildren<Button>();
    for(int i = 0; i < buttons.Length; i++)
    {
        var nav = new Navigation()
        {
            mode = Navigation.Mode.Explicit,
            selectOnUp = i > 0 ? buttons[i-1] : buttons[buttons.Length-1],
            selectOnDown = i < buttons.Length-1 ? buttons[i+1] : buttons[0]
        };
        buttons[i].navigation = nav;
    }
}
```