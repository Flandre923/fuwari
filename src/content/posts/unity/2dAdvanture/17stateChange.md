---
title: Untity学习记录-实现一个完整的2D游戏Demo-17-追击状态的切换
published: 2025-03-31
tags: ['Unity','游戏','demo','2d']
description: 使用Untiy时间一个2D冒险游戏demo
image: https://cdn.jsdelivr.net/gh/Flandre923/CDN@latest/img/20250331195820.png
category: Unity
draft: false
---
重点是利用有限状态机(FSM)来管理敌人的不同行为状态。我们将以野猪敌人为例，实现从巡逻到追击的完整状态转换逻辑。

## 敌人检测系统实现

### 物理检测方法

我们使用`Physics2D.BoxCast`来实现前方玩家检测：

```csharp
// Enemy.cs
[Header("检测参数")]
public Vector2 centerOffset = new Vector2(0, 0.65f);
public Vector2 checkSize = new Vector2(1, 1);
public float checkDistance = 4.5f;
public LayerMask attackLayer;

public bool FoundPlayer()
{
    Vector3 direction = new Vector3(-transform.localScale.x, 0, 0);
    RaycastHit2D hit = Physics2D.BoxCast(
        (Vector2)transform.position + centerOffset,
        checkSize,
        0,
        direction,
        checkDistance,
        attackLayer);
  
    return hit.collider != null;
}

// 可视化检测范围
void OnDrawGizmosSelected()
{
    Gizmos.DrawWireSphere(
        transform.position + (Vector3)centerOffset, 
        0.2f);
  
    Gizmos.DrawWireSphere(
        transform.position + (Vector3)centerOffset + 
        new Vector3(checkDistance * -transform.localScale.x, 0, 0),
        0.2f);
}
```

### 关键参数解析

1. **centerOffset**：检测起点偏移，确保从敌人中心发射
2. **checkSize**：检测盒尺寸，应覆盖玩家可能的站立区域
3. **checkDistance**：检测距离，控制敌人"视野"范围
4. **attackLayer**：指定检测玩家所在的层级

## 状态机系统优化

### 状态枚举定义

```csharp
// NPCState.cs
public enum NPCState
{
    Patrol,  // 巡逻状态
    Chase,   // 追击状态
    Skill    // 特殊技能状态(预留)
}
```

### 状态切换实现

```csharp
// Enemy.cs
public void SwitchState(NPCState newState)
{
    currentState?.OnExit();
  
    currentState = newState switch
    {
        NPCState.Patrol => patrolState,
        NPCState.Chase => chaseState,
        _ => patrolState
    };
  
    currentState?.OnEnter(this);
}
```

## 追击状态实现

### 追击状态核心逻辑

```csharp
// BoarChaseState.cs
public class BoarChaseState : BaseState
{
    public override void OnEnter(Enemy enemy)
    {
        base.OnEnter(enemy);
        currentEnemy.currentSpeed = currentEnemy.chaseSpeed;
        currentEnemy.anim.SetBool("run", true);
    }
  
    public override void LogicUpdate()
    {
        // 丢失玩家计时
        if (!currentEnemy.FoundPlayer())
        {
            currentEnemy.lostTimeCounter -= Time.deltaTime;
            if (currentEnemy.lostTimeCounter <= 0)
            {
                currentEnemy.SwitchState(NPCState.Patrol);
            }
        }
        else
        {
            currentEnemy.lostTimeCounter = currentEnemy.lostTime;
        }
    
        // 撞墙立即转向
        if (!currentEnemy.physicsCheck.isGround || 
            currentEnemy.physicsCheck.touchLeftWall || 
            currentEnemy.physicsCheck.touchRightWall)
        {
            currentEnemy.TurnAround();
        }
    }
  
    public override void PhysicsUpdate()
    {
        if (!currentEnemy.isHurt && !currentEnemy.isDead)
        {
            currentEnemy.Move();
        }
    }
  
    public override void OnExit()
    {
        currentEnemy.anim.SetBool("run", false);
        currentEnemy.currentSpeed = currentEnemy.normalSpeed;
        currentEnemy.lostTimeCounter = currentEnemy.lostTime;
    }
}
```

### 设计要点

1. **速度切换**：进入追击状态时提升移动速度
2. **动画控制**：播放奔跑动画增强视觉效果
3. **丢失计时**：玩家离开视野后开始倒计时
4. **立即转向**：撞墙或遇到悬崖不等待直接转向
5. **状态恢复**：退出时重置速度和计时器

## 受伤系统优化

### 击退效果改进

```csharp
// Enemy.cs
public virtual void OnTakeDamage(Transform attackTrans)
{
    // 停止当前X轴速度确保击退效果明显
    rb.velocity = new Vector2(0, rb.velocity.y);
  
    // 其余击退逻辑...
    StartCoroutine(OnHurt());
}
```

## 不同敌人类型设计思路

### 蜗牛敌人特点

1. **防御状态**：发现玩家后缩入壳中
2. **攻击免疫**：壳状态下不受攻击
3. **弱点攻击**：只能从背后攻击暴露的蜗牛

### 蜜蜂敌人特点

1. **自由移动**：不受地形限制的飞行模式
2. **追踪攻击**：平行于玩家时发动攻击
3. **快速反应**：受伤后短暂眩晕然后继续追击

## 性能优化建议

1. **检测频率控制**：适当降低BoxCast的执行频率
2. **状态对象池**：重用状态实例减少GC
3. **层级优化**：合理设置Layer减少不必要的碰撞检测
4. **动画优化**：使用Animator的Culling Mode

## 调试技巧

1. **可视化辅助**：使用Gizmos绘制检测范围
2. **状态日志**：在状态切换时输出调试信息
3. **参数调节**：暴露关键参数方便实时调整
4. **单独测试**：为每个状态创建独立测试场景

## 扩展思考

1. **混合状态**：结合多个状态的行为特点
2. **子状态机**：复杂状态进一步分解
3. **行为树**：更复杂的AI决策系统
4. **机器学习**：自适应难度调整

## 完整实现步骤

1. 创建基础状态抽象类
2. 实现具体状态(巡逻、追击等)
3. 设置状态切换接口
4. 实现玩家检测系统
5. 添加受伤和死亡逻辑
6. 调试和参数优化