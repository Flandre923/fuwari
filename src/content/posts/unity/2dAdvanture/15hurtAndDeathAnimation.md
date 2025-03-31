---
title: Untity学习记录-实现一个完整的2D游戏Demo-15-受伤和死亡的逻辑和动画
published: 2025-03-31
tags: ['Unity','游戏','demo','2d']
description: 使用Untiy时间一个2D冒险游戏demo
image: https://cdn.jsdelivr.net/gh/Flandre923/CDN@latest/img/20250331195820.png
category: Unity
draft: false
---
游戏中敌人受伤和死亡系统的实现。这个系统不仅能让游戏战斗更有反馈感，还能为玩家提供清晰的视觉提示。让我们从基础实现到高级技巧一步步深入。

## 受伤系统实现

### 攻击限制优化

首先，我们优化了玩家的攻击逻辑，限制只能在站立时攻击：

```csharp
// PlayerController.cs
public void OnAttack()
{
    if (!physicsCheck.isGround) 
        return; // 空中不能攻击
  
    // 攻击逻辑...
}
```

### 受伤动画制作

我们为野猪创建了两个关键动画：

1. **受伤动画(Hurt)** ：

    * 4帧闪烁效果
    * 采样率设置为10
    * 使用SpriteRenderer颜色变化增强效果
2. **死亡动画(Dead)** ：

    * 复制受伤动画并修改
    * 在Animation窗口中添加Color.a属性
    * 从1渐变到0实现淡出效果

### 受伤逻辑代码

```csharp
// Enemy.cs
[Header("受伤参数")]
public float hurtForce = 4.5f;
public float hurtDuration = 0.45f;

protected bool isHurt;
protected Transform attacker;

public virtual void OnTakeDamage(Transform attackTrans)
{
    attacker = attackTrans;
  
    // 判断攻击方向并转向
    if (attackTrans.position.x > transform.position.x)
    {
        transform.localScale = new Vector3(-1, 1, 1);
    }
    else if (attackTrans.position.x < transform.position.x)
    {
        transform.localScale = new Vector3(1, 1, 1);
    }
  
    // 击退效果
    isHurt = true;
    anim.SetTrigger("hurt");
    Vector2 dir = (transform.position - attackTrans.position).normalized;
    rb.AddForce(dir * hurtForce, ForceMode2D.Impulse);
  
    // 启动协程
    StartCoroutine(OnHurt());
}

protected virtual IEnumerator OnHurt()
{
    yield return new WaitForSeconds(hurtDuration);
    isHurt = false;
}
```

### 关键点解析

1. **方向判断**：根据攻击者位置决定转向方向
2. **物理击退**：使用`AddForce`实现击退效果
3. **协程控制**：确保击退动画完整播放
4. **状态管理**：`isHurt`变量控制受伤状态行为

## 死亡系统实现

### 死亡逻辑代码

```csharp
// Enemy.cs
protected bool isDead;

public virtual void OnDie()
{
    isDead = true;
    gameObject.layer = 2; // 切换到IgnoreRaycast层
    anim.SetBool("dead", true);
  
    // 禁用碰撞体和组件
    if (TryGetComponent<Collider2D>(out var collider))
        collider.enabled = false;
    if (TryGetComponent<Rigidbody2D>(out var rb))
        rb.simulated = false;
}

// 动画事件调用的方法
public void DestroyAfterAnimation()
{
    Destroy(gameObject);
}
```

### 动画事件设置

1. 在死亡动画最后一帧添加Animation Event
2. 调用`DestroyAfterAnimation`方法
3. 确保物体在动画播放完后销毁

### 碰撞层优化

通过修改Layer避免死亡动画期间与玩家碰撞：

```csharp
// 修改Project Settings → Physics2D → Layer Collision Matrix
// 取消Player和IgnoreRaycast层的交叉勾选
```

## 高级技巧：协程(Coroutine)详解

协程是我们实现时序控制的重要工具：

```csharp
// 协程基本结构
IEnumerator MyCoroutine()
{
    // 第一步执行
    yield return null; // 等待下一帧
  
    // 第二步执行
    yield return new WaitForSeconds(1f); // 等待1秒
  
    // 第三步执行
    yield return new WaitUntil(() => condition); // 等待条件满足
}

// 启动协程
StartCoroutine(MyCoroutine());
```

在敌人系统中的实际应用：

* 控制击退持续时间
* 管理无敌时间
* 实现死亡动画延迟销毁


## 设计思考与最佳实践

1. **视觉反馈**：受伤闪烁和死亡淡出提供清晰状态提示
2. **物理反馈**：击退效果增强打击感
3. **性能优化**：死亡后及时禁用碰撞体和物理模拟
4. **扩展性**：`virtual`方法允许子类定制行为
5. **安全性**：Layer管理避免不必要的碰撞检测


## 常见问题与解决方案

**Q: 为什么我的敌人死亡后还能攻击玩家？** 
A: 确保：

1. 正确设置了Layer Collision Matrix
2. 死亡后及时禁用碰撞体(`collider.enabled = false`)
3. 检查是否有其他碰撞检测逻辑遗漏

**Q: 击退效果不明显怎么办？** 
A: 调整两个参数：

1. `hurtForce` - 增加击退力度
2. `hurtDuration` - 延长击退时间

**Q: 如何实现不同类型的死亡效果？** 
A: 在子类中重写`OnDie`方法：

```csharp
public class Boar : Enemy
{
    public override void OnDie()
    {
        // 自定义死亡逻辑
        base.OnDie(); // 调用父类基础逻辑
    }
}
```

## 性能优化建议

1. **对象池**：频繁创建销毁的敌人使用对象池
2. **动画优化**：使用Animator的Culling Mode减少不可见动画更新
3. **碰撞精简**：死亡后及时禁用所有物理组件
4. **事件替代Update**：用动画事件替代每帧检测