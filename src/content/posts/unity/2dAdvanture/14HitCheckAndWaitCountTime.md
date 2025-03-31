---
title: Untity学习记录-实现一个完整的2D游戏Demo-14-撞墙判定和等候倒计时
published: 2025-03-31
tags: ['Unity','游戏','demo','2d']
description: 使用Untiy时间一个2D冒险游戏demo
image: https://cdn.jsdelivr.net/gh/Flandre923/CDN@latest/img/20250331195820.png
category: Unity
draft: false
---
墙体检测以及基于此实现的敌人巡逻AI。这个功能不仅适用于敌人，也可以用在玩家角色上实现蹬墙跳等酷炫动作。

## 墙体检测系统设计

### PhysicsCheck组件扩展

我们首先扩展了之前用于地面检测的`PhysicsCheck`组件，添加了墙体检测功能：

```csharp
public class PhysicsCheck : MonoBehaviour
{
    [Header("墙体检测")]
    public bool manual; // 是否手动设置检测点
    public float checkRadius = 0.1f;
    public Vector2 leftOffset;
    public Vector2 rightOffset;
    public LayerMask groundLayer;
  
    [HideInInspector] public bool touchLeftWall;
    [HideInInspector] public bool touchRightWall;
  
    private CapsuleCollider2D coll;
  
    void Awake()
    {
        coll = GetComponent<CapsuleCollider2D>();
    
        if (!manual)
        {
            // 自动计算检测点偏移
            rightOffset = new Vector2(
                (coll.bounds.size.x + coll.offset.x) / 2,
                coll.bounds.size.y / 2);
        
            leftOffset = new Vector2(
                -rightOffset.x,
                rightOffset.y);
        }
    }
  
    void FixedUpdate()
    {
        // 墙体检测
        touchLeftWall = Physics2D.OverlapCircle(
            (Vector2)transform.position + leftOffset,
            checkRadius, groundLayer);
        
        touchRightWall = Physics2D.OverlapCircle(
            (Vector2)transform.position + rightOffset,
            checkRadius, groundLayer);
    }
  
    // 在Scene视图绘制检测范围
    void OnDrawGizmosSelected()
    {
        Gizmos.DrawWireSphere((Vector2)transform.position + leftOffset, checkRadius);
        Gizmos.DrawWireSphere((Vector2)transform.position + rightOffset, checkRadius);
    }
}
```

### 关键点解析

1. **自动/手动检测点设置**：

    * 手动模式：在Inspector中直接设置偏移量
    * 自动模式：根据碰撞体尺寸和偏移自动计算
2. **碰撞体几何类型选择**：

    * 使用`Polygon`而非`Outlines`确保完整碰撞检测
    * 避免快速移动时穿模导致的检测失败

## 敌人巡逻AI实现

### 计时器系统

```csharp
[Header("计时器")]
public float waitTime = 2f; // 等待时间
private float waitTimeCounter;
private bool isWaiting;
```

### 巡逻状态逻辑

```csharp
void Update()
{
    // 检测是否需要转向
    if (!isWaiting && (faceDirection.x < 0 && physicsCheck.touchLeftWall ||
                      faceDirection.x > 0 && physicsCheck.touchRightWall))
    {
        isWaiting = true;
        waitTimeCounter = waitTime;
        anim.SetBool("walk", false); // 停止行走动画
    }
  
    // 计时器逻辑
    if (isWaiting)
    {
        waitTimeCounter -= Time.deltaTime;
        if (waitTimeCounter <= 0)
        {
            isWaiting = false;
            TurnAround(); // 转向
            anim.SetBool("walk", true); // 恢复行走动画
        }
    }
}

void TurnAround()
{
    // 转向逻辑
    Vector3 scale = transform.localScale;
    scale.x *= -1;
    transform.localScale = scale;
  
    // 更新面朝方向
    faceDirection = new Vector3(-transform.localScale.x, 0, 0);
}
```

### 设计思考与优化

1. **方向敏感检测**：

    * 只有当敌人朝向与碰撞方向一致时才触发转向
    * 避免转身后立即再次触发转向的问题
2. **状态分离**：

    * 行走状态与等待状态明确分离
    * 状态转换通过计时器平滑过渡
3. **动画同步**：

    * 等待时停止行走动画
    * 转向后恢复行走动画

## 常见问题与解决方案

**Q: 为什么我的敌人检测不到墙壁？** 
A: 检查以下几点：

1. 确保墙壁碰撞体的`Geometry Type`设置为`Polygon`
2. 确认检测层的`LayerMask`设置正确
3. 检查检测半径(`checkRadius`)是否合适

**Q: 敌人转向后立即又转回来怎么办？** 
A: 这是因为转身后另一侧的检测点也碰到了墙。解决方案：

1. 添加方向判断：`faceDirection.x < 0 && touchLeftWall`
2. 调整检测点偏移量，避免过于靠近碰撞体边缘

**Q: 如何让不同敌人有不同的等待时间？** 
A: 在具体敌人类中重写`waitTime`变量：

```csharp
public class Boar : Enemy
{
    new public float waitTime = 3f; // 野猪等待3秒
  
    void Start()
    {
        base.waitTime = waitTime; // 初始化父类变量
    }
}
```

## 性能优化建议

1. **减少检测频率**：

    * 将`FixedUpdate`中的检测改为每几帧执行一次
    * 使用`Physics2D.OverlapCircleNonAlloc`避免GC
2. **对象池管理**：

    * 对频繁创建销毁的敌人使用对象池
    * 复用物理检测组件
3. **层级剔除**：

    * 对远离玩家的敌人降低检测频率
    * 使用`Physics2D.autoSyncTransforms`优化