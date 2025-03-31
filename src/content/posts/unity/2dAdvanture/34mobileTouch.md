---
title: Untity学习记录-实现一个完整的2D游戏Demo-34-实现移动设备屏幕操作
published: 2025-03-31
tags: ['Unity','游戏','demo','2d']
description: 使用Untiy时间一个2D冒险游戏demo
image: https://cdn.jsdelivr.net/gh/Flandre923/CDN@latest/img/20250331195820.png
category: Unity
draft: false
---
在Unity中使用Input System实现跨平台的触控输入系统，让你的游戏能够完美适配移动设备。这个教程将带你从零开始创建虚拟摇杆和屏幕按钮，并实现平台自动检测功能。

## 1. 虚拟摇杆实现

### 1.1 创建虚拟摇杆UI

首先，我们需要在Canvas下创建虚拟摇杆的基础结构：

```csharp

1. 在Main Canvas下创建空对象，命名为"MobileTouch"
2. 添加Image组件作为摇杆背景，命名为"StickBackground"
3. 添加Image组件作为摇杆手柄，命名为"StickHandle"
4. 调整Anchor Preset为左下角，方便移动设备操作
```

```csharp
// 摇杆尺寸设置建议
stickBackground.rectTransform.sizeDelta = new Vector2(300, 300);
stickHandle.rectTransform.sizeDelta = new Vector2(150, 150);
stickBackground.color = new Color(1, 1, 1, 0.5f); // 半透明效果
```

### 1.2 添加On-Screen Stick组件

使用Unity的Input System提供的OnScreen组件：

```csharp
// 为摇杆背景添加OnScreenStick组件
var onScreenStick = stickBackground.gameObject.AddComponent<OnScreenStick>();

// 配置摇杆参数
onScreenStick.movementRange = 100f; // 摇杆移动范围
onScreenStick.controlPath = "<Gamepad>/leftStick"; // 映射到左摇杆
```

## 2. 虚拟按钮实现

### 2.1 创建按钮布局

在Canvas右侧创建四个虚拟按钮：

```csharp
1. 在MobileTouch下创建空对象"RightPanel"
2. 添加四个Image作为按钮，分别命名为：
   - "ButtonNorth" (上/红色)
   - "ButtonEast" (右/绿色)
   - "ButtonSouth" (下/蓝色)
   - "ButtonWest" (左/黄色)
3. 使用Anchor Preset精确定位每个按钮
```

### 2.2 添加On-Screen Button组件

```csharp
// 为每个按钮添加OnScreenButton并映射到对应输入
void SetupVirtualButton(GameObject button, string controlPath)
{
    var onScreenBtn = button.AddComponent<OnScreenButton>();
    onScreenBtn.controlPath = controlPath;
}

// 按钮映射配置
SetupVirtualButton(buttonNorth, "<Gamepad>/buttonNorth"); // X/Y按钮
SetupVirtualButton(buttonEast, "<Gamepad>/buttonEast");   // B/圆形按钮
SetupVirtualButton(buttonSouth, "<Gamepad>/buttonSouth"); // A/交叉按钮
SetupVirtualButton(buttonWest, "<Gamepad>/buttonWest");   // Y/方形按钮
```

## 3. 平台自动检测与UI切换

### 3.1 检测运行平台

```csharp
public class PlatformDetector : MonoBehaviour
{
    public GameObject mobileTouchUI;
  
    private void Awake()
    {
#if UNITY_STANDALONE
        // PC/Mac平台隐藏触控UI
        mobileTouchUI.SetActive(false);
#elif UNITY_IOS || UNITY_ANDROID
        // 移动平台显示触控UI
        mobileTouchUI.SetActive(true);
    
        // 移动平台特有设置
        Application.targetFrameRate = 60;
        Screen.orientation = ScreenOrientation.LandscapeLeft;
#endif
    }
}
```

### 3.2 优化移动设备输入

```csharp
// 为移动设备优化输入响应
public class MobileInputOptimizer : MonoBehaviour
{
    [SerializeField] private float stickDeadZone = 0.2f;
    [SerializeField] private float buttonPressScale = 0.9f;
  
    private void OnEnable()
    {
        var stick = GetComponent<OnScreenStick>();
        if(stick != null)
        {
            stick.deadZone = stickDeadZone;
        }
    }
  
    public void OnButtonPressed(Image buttonImage)
    {
        // 按钮按下效果
        buttonImage.transform.localScale = Vector3.one * buttonPressScale;
    }
  
    public void OnButtonReleased(Image buttonImage)
    {
        // 按钮释放效果
        buttonImage.transform.localScale = Vector3.one;
    }
}
```

## 4. Input System配置优化

### 4.1 创建移动设备专用的Input Action Asset

```csharp
// 步骤：
1. 在Project窗口右键 -> Create -> Input Actions
2. 命名为"MobileInput"
3. 双击打开Input Action Editor
4. 添加"Touch" Action Map
5. 配置触控相关输入：
   - PrimaryTouch (触摸位置)
   - SecondaryTouch (双指操作)
   - Tap (点击)
   - Swipe (滑动)
```

### 4.2 在代码中处理触控输入

```csharp
public class MobileInputHandler : MonoBehaviour
{
    private InputAction primaryTouchAction;
  
    private void Awake()
    {
        var playerInput = GetComponent<PlayerInput>();
        primaryTouchAction = playerInput.actions["Touch/PrimaryTouch"];
    }
  
    private void OnEnable()
    {
        primaryTouchAction.performed += OnPrimaryTouch;
        primaryTouchAction.canceled += OnPrimaryTouchEnd;
    }
  
    private void OnPrimaryTouch(InputAction.CallbackContext context)
    {
        Vector2 touchPos = context.ReadValue<Vector2>();
        Debug.Log($"Primary touch at: {touchPos}");
    }
  
    private void OnPrimaryTouchEnd(InputAction.CallbackContext context)
    {
        Debug.Log("Primary touch ended");
    }
}
```

## 5. 多平台构建设置

### 5.1 安装必要平台模块

```csharp
1. 打开Unity Hub
2. 选择当前项目使用的Unity版本
3. 点击右上角齿轮图标 -> Add Modules
4. 勾选需要添加的平台：
   - Android Build Support
   - iOS Build Support
   - WebGL Build Support
5. 点击Install完成安装
```

### 5.2 切换构建平台

```csharp
// 在代码中动态获取当前平台
RuntimePlatform platform = Application.platform;

// 平台特定逻辑
if(platform == RuntimePlatform.Android || platform == RuntimePlatform.IPhonePlayer)
{
    // 移动设备特有逻辑
}
else if(platform == RuntimePlatform.WebGLPlayer)
{
    // WebGL特有逻辑
}
```

## 6. 高级触控功能扩展

### 6.1 实现手势识别

```csharp
public class GestureRecognizer : MonoBehaviour
{
    private Vector2 touchStartPos;
    private float touchStartTime;
  
    private void Update()
    {
        if(Input.touchCount > 0)
        {
            Touch touch = Input.GetTouch(0);
        
            switch(touch.phase)
            {
                case TouchPhase.Began:
                    touchStartPos = touch.position;
                    touchStartTime = Time.time;
                    break;
                
                case TouchPhase.Ended:
                    DetectGesture(touch.position);
                    break;
            }
        }
    }
  
    private void DetectGesture(Vector2 endPos)
    {
        float distance = Vector2.Distance(touchStartPos, endPos);
        float duration = Time.time - touchStartTime;
    
        if(distance > 100f && duration < 0.5f)
        {
            Vector2 direction = (endPos - touchStartPos).normalized;
        
            if(Mathf.Abs(direction.x) > Mathf.Abs(direction.y))
            {
                Debug.Log(direction.x > 0 ? "向右滑动" : "向左滑动");
            }
            else
            {
                Debug.Log(direction.y > 0 ? "向上滑动" : "向下滑动");
            }
        }
    }
}
```

### 6.2 虚拟摇杆动态调整

```csharp
public class DynamicJoystick : OnScreenStick
{
    [SerializeField] private float activeOpacity = 0.7f;
    [SerializeField] private float inactiveOpacity = 0.3f;
  
    private Image stickImage;
  
    protected override void Start()
    {
        base.Start();
        stickImage = GetComponent<Image>();
    }
  
    public override void OnPointerDown(PointerEventData eventData)
    {
        base.OnPointerDown(eventData);
        stickImage.color = new Color(1, 1, 1, activeOpacity);
    }
  
    public override void OnPointerUp(PointerEventData eventData)
    {
        base.OnPointerUp(eventData);
        stickImage.color = new Color(1, 1, 1, inactiveOpacity);
    }
}
```

## 7. 总结与最佳实践

通过本教程，我们实现了：

1. **完整的虚拟摇杆系统**：支持8方向输入和力度控制
2. **多功能虚拟按钮**：映射到游戏手柄标准布局
3. **智能平台检测**：自动适配PC和移动设备
4. **手势识别扩展**：为高级操作提供可能

**最佳实践建议**：

1. 为不同屏幕尺寸设计响应式UI布局
2. 添加触觉反馈增强移动设备体验
3. 考虑左右手操作习惯提供自定义布局选项
4. 为虚拟按钮添加按下/释放的视觉反馈
5. 在Editor中模拟触控输入方便测试