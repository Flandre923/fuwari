---
title: Untity学习记录-实现一个完整的2D游戏Demo-13-基本的移动逻辑和动画逻辑
published: 2025-03-31
tags: ['Unity','游戏','demo','2d']
description: 使用Untiy时间一个2D冒险游戏demo
image: https://cdn.jsdelivr.net/gh/Flandre923/CDN@latest/img/20250331195820.png
category: Unity
draft: false
---
## 基础架构：类继承设计

首先，我们需要创建一个基础敌人类`Enemy`，作为所有敌人类型的父类：

```csharp
public class Enemy : MonoBehaviour
{
    // 移动速度参数
    public float normalSpeed;
    public float chaseSpeed;
    protected float currentSpeed;
  
    // 组件引用
    protected Rigidbody2D rb;
    protected Animator anim;
    protected Vector3 faceDirection;
  
    void Awake()
    {
        rb = GetComponent<Rigidbody2D>();
        anim = GetComponent<Animator>();
    }
  
    protected virtual void Move()
    {
        faceDirection = new Vector3(-transform.localScale.x, 0, 0);
        rb.velocity = new Vector2(faceDirection.x * currentSpeed, rb.velocity.y);
    }
  
    void FixedUpdate()
    {
        Move();
    }
}
```

### 继承与重写

然后，我们可以创建具体的敌人类型，比如野猪(Boar)，继承自`Enemy`类：

```csharp
public class Boar : Enemy
{
    void Start()
    {
        currentSpeed = normalSpeed;
    }
  
    protected override void Move()
    {
        base.Move(); // 调用父类的Move方法
        anim.SetBool("walk", true); // 添加特有行为
    }
}
```

这里有几个关键点：

1. `protected`修饰符使子类可以访问父类的成员
2. `virtual`关键字允许方法被重写
3. `override`关键字表示重写父类方法
4. `base.Move()`调用父类的原始实现

## 动画状态机配置

在Animator Controller中，我们为野猪设置了三个基本状态：

1. **Idle** - 站立不动
2. **Walk** - 巡逻移动
3. **Run** - 追击玩家

状态转换通过布尔参数控制：

```csharp
// 在需要切换状态时调用
anim.SetBool("walk", isWalking);
anim.SetBool("run", isChasing);
```

状态机转换条件设置：

* Idle → Walk: `walk = true`
* Walk → Run: `run = true`
* Run → Walk: `run = false && walk = true`
* 任何状态 → Idle: `walk = false && run = false`

## 设计思考与最佳实践

1. **封装性**：使用`protected`而非`public`保护关键变量，只对子类可见
2. **扩展性**：通过`virtual`方法允许子类扩展而非修改父类行为
3. **代码复用**：将通用功能放在父类中，避免重复代码
4. **状态管理**：使用Animator参数而非直接速度值控制动画，适应不同敌人类型

## 常见问题与解决方案

**Q: 为什么我的子类无法访问父类的变量？** 
A: 确保变量使用`protected`而非`private`修饰符

**Q: 重写方法时为什么要调用base.Method()？** 
A: 这样可以保留父类的原始行为，只在基础上添加新功能

**Q: 如何设计适应不同敌人的动画系统？** 
A: 使用状态参数而非具体数值控制转换，使每种敌人可以定义自己的速度阈值