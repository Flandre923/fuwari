---
title: Untity学习记录-实现一个完整的2D游戏Demo-16-有限状态机和抽象多态类
published: 2025-03-31
tags: ['Unity','游戏','demo','2d']
description: 使用Untiy时间一个2D冒险游戏demo
image: https://cdn.jsdelivr.net/gh/Flandre923/CDN@latest/img/20250331195820.png
category: Unity
draft: false
---
有限状态机(FSM)的实现，以及如何使用抽象类来构建灵活可扩展的敌人AI系统。

## 什么是有限状态机？

有限状态机是一种数学模型，用于表示对象在不同状态下的行为。在游戏开发中，它特别适合用来管理角色的各种行为状态，比如：

* 巡逻状态
* 追击状态
* 攻击状态
* 受伤状态
* 死亡状态

### 状态机的基本组成

1. **状态(State)** ：角色当前的行为模式
2. **转换(Transition)** ：状态之间的切换条件
3. **动作(Action)** ：在特定状态下执行的行为

## 抽象类基础

### 抽象类 vs 普通类

```csharp
// 普通类
public class Pizza 
{
    public virtual void MakeCrust() 
    {
        // 基础饼皮制作
    }
}

// 抽象类
public abstract class PizzaBase
{
    public abstract void MakeCrust(); // 只有声明没有实现
}
```

抽象类的特点：

* 不能被实例化
* 可以包含抽象方法（只有声明没有实现）
* 派生类必须实现所有抽象方法
* 可以包含已实现的方法

### 为什么要用抽象类实现状态机？

1. **强制规范**：确保所有状态都有统一接口
2. **灵活扩展**：方便添加新状态
3. **代码复用**：共享通用逻辑
4. **清晰结构**：状态逻辑分离，易于维护

## 实现步骤

### 1. 创建抽象基类

```csharp
// BaseState.cs
public abstract class BaseState
{
    protected Enemy currentEnemy;
  
    public virtual void OnEnter(Enemy enemy) 
    {
        currentEnemy = enemy;
    }
  
    public abstract void LogicUpdate();
    public abstract void PhysicsUpdate();
    public abstract void OnExit();
}
```

### 2. 实现具体状态

以巡逻状态为例：

```csharp
// BoarPatrolState.cs
public class BoarPatrolState : BaseState
{
    public override void LogicUpdate()
    {
        // 巡逻逻辑
        if (!currentEnemy.physicsCheck.isGround || 
            currentEnemy.physicsCheck.touchLeftWall || 
            currentEnemy.physicsCheck.touchRightWall)
        {
            currentEnemy.isWaiting = true;
            currentEnemy.anim.SetBool("walk", false);
        }
    
        // 计时器逻辑
        if (currentEnemy.isWaiting)
        {
            currentEnemy.waitTimeCounter -= Time.deltaTime;
            if (currentEnemy.waitTimeCounter <= 0)
            {
                currentEnemy.isWaiting = false;
                currentEnemy.TurnAround();
                currentEnemy.anim.SetBool("walk", true);
            }
        }
    
        // TODO: 检测玩家
    }
  
    public override void PhysicsUpdate()
    {
        if (!currentEnemy.isHurt && !currentEnemy.isDead && !currentEnemy.isWaiting)
        {
            currentEnemy.Move();
        }
    }
  
    public override void OnExit()
    {
        currentEnemy.anim.SetBool("walk", false);
    }
}
```

### 3. 在Enemy类中管理状态

```csharp
// Enemy.cs
public class Enemy : MonoBehaviour
{
    public BaseState patrolState;
    public BaseState chaseState;
    protected BaseState currentState;
  
    protected virtual void Awake()
    {
        patrolState = new BoarPatrolState();
        // 初始化其他状态...
    }
  
    protected virtual void OnEnable()
    {
        currentState = patrolState;
        currentState.OnEnter(this);
    }
  
    void Update()
    {
        currentState.LogicUpdate();
    }
  
    void FixedUpdate()
    {
        currentState.PhysicsUpdate();
    }
  
    protected virtual void OnDisable()
    {
        currentState.OnExit();
    }
  
    public void SwitchState(BaseState newState)
    {
        currentState.OnExit();
        currentState = newState;
        currentState.OnEnter(this);
    }
}
```

## 设计优势

1. **单一职责原则**：每个状态只关注自己的逻辑
2. **开闭原则**：添加新状态不影响现有代码
3. **低耦合**：状态之间通过明确条件转换
4. **易调试**：可以轻松追踪当前状态

## 状态转换示例

```csharp
// 在巡逻状态中检测到玩家
if (DetectPlayer())
{
    currentEnemy.SwitchState(currentEnemy.chaseState);
}

// 在追击状态中丢失玩家
if (!DetectPlayer() && chaseTime > maxChaseTime)
{
    currentEnemy.SwitchState(currentEnemy.patrolState);
}
```

## 性能优化技巧

1. **对象池**：重用状态实例而非频繁创建销毁
2. **状态缓存**：常用状态预先实例化
3. **分层状态机**：复杂AI使用多层状态机
4. **条件优化**：使用位掩码优化多条件判断

## 常见问题解决

**Q: 为什么我的状态切换不生效？** 
A: 检查：

1. 确保调用了`SwitchState`方法
2. 新状态的`OnEnter`方法正确执行
3. 转换条件逻辑正确

**Q: 如何共享数据 between states？** 
A: 通过Enemy基类中的protected变量共享

**Q: 状态太多导致代码混乱怎么办？** 
A: 考虑：

1. 使用子状态机
2. 将相关状态分组
3. 使用脚本化对象管理状态配置

## 扩展思考

1. **状态模式 vs 策略模式**：两者相似但意图不同
2. **行为树**：更复杂的AI可以考虑行为树
3. **Utility AI**：基于得分的决策系统
4. **机器学习**：高级AI可以使用ML算法