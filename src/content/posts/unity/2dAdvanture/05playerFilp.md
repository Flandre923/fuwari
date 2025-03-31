---
title: Untity学习记录-实现一个完整的2D游戏Demo-05-实现人物的移动和反转
published: 2025-03-31
tags: ['Unity','游戏','demo','2d']
description: 使用Untiy时间一个2D冒险游戏demo
image: https://cdn.jsdelivr.net/gh/Flandre923/CDN@latest/img/20250331195820.png
category: Unity
draft: false
---
**一、使用 Rigidbody 2D 的 Velocity 进行移动：**

1. **Rigidbody 驱动坐标：**  强调一旦物体挂载了 Rigidbody 2D 组件，其位置和旋转主要由 Rigidbody 控制，可以通过查看 Rigidbody 组件的 Info 面板来了解其 `position`、`rotation` 和 `velocity`。
2. **使用速度驱动移动：**  相较于直接修改 Transform 的坐标，通过修改 Rigidbody 的 `velocity` 来实现物理效果更真实的移动。
3. **创建 Speed 变量：**  创建一个 `public float speed` 变量，用于控制角色的移动速度，可以在 Unity 编辑器中调整。
4. **在 FixedUpdate 中执行物理操作：**  解释了 `FixedUpdate()` 函数以固定的时间间隔执行，适合进行物理相关的操作，确保在不同性能的设备上表现一致。
5. **创建 Move 函数：**  创建一个自定义的 `void Move()` 函数来包含角色移动的逻辑。
6. **在 FixedUpdate 中调用 Move 函数：**  在 `FixedUpdate()` 函数中调用 `Move()` 函数，使移动逻辑在固定的物理更新周期内执行。

**二、获取 Rigidbody 2D 组件：**

1. **方法一：在 Inspector 中拖拽：**

    * 声明一个 `public Rigidbody2D rb` 变量。
    * 将 Player 对象上的 Rigidbody 2D 组件直接拖拽到 Inspector 窗口中 `rb` 变量的槽位中。
    * 优点：获取速度快，适用于需要在游戏开始前就确定引用的情况。
2. **方法二：通过代码获取 (GetComponent)：**

    * 声明一个 `private Rigidbody2D rb` 变量。
    * 在 `Awake()` 函数中使用 `rb = GetComponent<Rigidbody2D>();` 来获取自身 GameObject 上的 Rigidbody 2D 组件。
    * 解释了 `public` (公开，可在 Inspector 和其他脚本访问) 和 `private` (私有，仅限当前脚本访问) 关键字的区别。

**三、修改 Rigidbody 2D 的速度 (Velocity)：**

1. **访问 Velocity：**  通过 `rb.velocity` 访问 Rigidbody 2D 的速度属性。
2. **设置新的速度：**  将 `velocity` 设置为一个新的 `Vector2` 值：`new Vector2(inputDirection.x * speed * Time.deltaTime, rb.velocity.y);`。

    * **​`inputDirection.x`​**​ **：**  获取水平方向的输入值 (-1, 0, 1 或手柄的模拟值)。
    * **​`speed`​**​ **：**  应用设定的移动速度。
    * **​`Time.deltaTime`​**​ **：**  使移动速度与帧率无关，保证在不同性能的设备上速度一致。
    * **​`rb.velocity.y`​**​ **：**  保留原有的垂直方向速度，以保持重力效果。

**四、实现角色翻转：**

1. **翻转的两种方法：**

    * 修改 `transform.localScale.x` 的值（例如设置为 -1 可以实现水平翻转）。
    * 使用 `SpriteRenderer` 组件的 `flipX` 属性。
2. **使用** **​`transform.localScale.x`​** **进行翻转：**

    * **访问 Transform 组件：**  通过 `transform` (小写) 直接访问 GameObject 的 Transform 组件。
    * **创建** **​`faceDirection`​** **变量：**  创建一个 `int faceDirection` 变量来记录角色的朝向 (1 为右，-1 为左)。
    * **初始化** **​`faceDirection`​**​ **：**  将 `faceDirection` 初始化为当前的 `transform.localScale.x` (强制转换为 `int`)。
    * **根据输入方向更新** **​`faceDirection`​**​ **：**  使用 `if` 语句判断 `inputDirection.x` 的正负来更新 `faceDirection` 的值。
    * **应用翻转：**  设置 `transform.localScale = new Vector3(faceDirection, 1, 1);` 来根据 `faceDirection` 的值翻转角色。

**五、关于** **​`SpriteRenderer.flipX`​**​ **：**

1. **获取** **​`SpriteRenderer`​** **组件：**  可以使用 `GetComponent<SpriteRenderer>()` 来获取角色身上的 `SpriteRenderer` 组件。
2. **使用** **​`flipX`​** **属性：**  通过 `GetComponent<SpriteRenderer>().flipX = true;` 或 `false;` 来控制是否在 X 轴翻转精灵。

**六、代码周期函数的重要性：**

1. **​`Update()`​**​ **：**  每帧执行。
2. **​`FixedUpdate()`​**​ **：**  以固定的时间间隔执行，用于物理相关的操作。
3. **​`OnEnable()`​**  **和** **​`OnDisable()`​**​ **：**  在 GameObject 启用和禁用时执行。
4. **​`Awake()`​**​ **：**  在 `Start()` 函数之前执行。