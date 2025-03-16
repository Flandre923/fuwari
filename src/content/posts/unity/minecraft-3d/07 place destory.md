---
title: Untity学习记录-实现我的世界渲染和简单的基于噪声的世界生成-07-破坏和放置
published: 2025-03-16
tags: ['Unity','游戏']
description: 使用unity实现的我的世界类似的世界生成方法，基于噪声。实现面剔除的渲染。
image: https://cdn.jsdelivr.net/gh/Flandre923/CDN@latest/img/20250316141422.png
category: Unity
draft: false
---
# 使用 MyTerrainModifier1 脚本与你的程序化地形互动

在这篇博客中，我们将深入了解 `MyTerrainModifier1` 脚本，这个脚本允许玩家与我们之前创建的程序化地形进行交互，例如破坏和放置方块。记住，这个脚本需要**挂载到你场景中负责玩家视角的相机上**，这样射线才能从正确的方向发射。

## 脚本概览

`MyTerrainModifier1` 是一个 Unity C# 脚本，它通过监听鼠标点击事件并进行射线投射来检测玩家想要交互的地形方块。然后，它会根据玩家的输入（左键或右键）执行相应的操作，并与我们的物品栏系统 (`MyInventory`) 进行交互。

```csharp
using UnityEngine;

public class MyTerrainModifier1 : MonoBehaviour
{
    public LayerMask groundLayer;
    public MyInventory inv;
    private float maxDist = 4;

    private void Start()
    {
    }

    private void Update()
    {
        bool leftClick = Input.GetMouseButtonDown(0);
        bool rightClick = Input.GetMouseButtonDown(1);

        if (leftClick || rightClick)
        {
            RaycastHit hitInfo;
            if (Physics.Raycast(transform.position, transform.forward, out hitInfo, maxDist, groundLayer))
            {
                Debug.Log("Left or right mouse button was clicked."); // 测试鼠标点击事件

                Vector3 pointInTargetBlock;

                if (rightClick)
                    pointInTargetBlock = hitInfo.point + transform.forward * .01f;
                else
                    pointInTargetBlock = hitInfo.point - transform.forward * .01f;

                int chunkPosX = Mathf.FloorToInt(pointInTargetBlock.x / 16f) * 16;
                int chunkPosZ = Mathf.FloorToInt(pointInTargetBlock.z / 16f) * 16;

                MyChunkPos cp = new MyChunkPos(chunkPosX, chunkPosZ);
                MyTerrainChunk tc = MyTerrainGenerator.chunks[cp];

                int bix = Mathf.FloorToInt(pointInTargetBlock.x) - chunkPosX + 1;
                int biz = Mathf.FloorToInt(pointInTargetBlock.z) - chunkPosZ + 1;
                int biy = Mathf.FloorToInt(pointInTargetBlock.y);

                if (rightClick)
                {
                    inv.AddToInventory(tc.blocks[bix,biy,biz]);
                    tc.blocks[bix, biy, biz] = MyBLockType.Air;
                    tc.BuildMesh();
                }
                else if(leftClick)
                {
                    if (inv.CanPlaceCur())
                    {
                        tc.blocks[bix, biy, biz] = inv.GetCurBlock();
                        tc.BuildMesh();
                        inv.ReduceCur();
                    }
                }
            }
        }
    }
}
```

## 脚本属性详解

* **​`groundLayer`​**  **(LayerMask):**  这是一个图层蒙版，用于指定射线投射应该检测哪些图层上的物体。你需要确保你的地形所在的图层被包含在这个蒙版中。你可以在 Unity 编辑器的 Inspector 窗口中设置这个属性。
* **​`inv`​**  **(MyInventory):**  这是对我们之前创建的 `MyInventory` 脚本的引用。`MyTerrainModifier1` 脚本需要与物品栏系统进行交互，以便在破坏方块时添加物品，以及在放置方块时获取当前选中的方块类型。你需要在 Inspector 窗口中将你的 `MyInventory` 脚本实例拖拽到这个属性上。
* **​`maxDist`​**  **(float):**  这个变量定义了射线投射的最大距离。玩家只有在距离地形 `maxDist` 以内的位置点击鼠标，才能触发交互。当前设置为 4。

## `Start()` 方法

目前这个方法是空的，你可以在这里添加一些初始化的逻辑，如果需要的话。

## `Update()` 方法详解

`Update()` 方法是 Unity 中每一帧都会执行的方法，它负责检测玩家的输入并执行相应的操作：

1. **检测鼠标点击:**

    * `bool leftClick = Input.GetMouseButtonDown(0);` 检测鼠标左键是否被按下。
    * `bool rightClick = Input.GetMouseButtonDown(1);` 检测鼠标右键是否被按下。
2. **进行射线投射:**

    * `if (leftClick || rightClick)`: 只有在检测到鼠标点击时，才会执行后续的交互逻辑。
    * `RaycastHit hitInfo;`: 声明一个 `RaycastHit` 类型的变量，用于存储射线投射的结果。
    * `if (Physics.Raycast(transform.position, transform.forward, out hitInfo, maxDist, groundLayer))`:  这是进行射线投射的关键代码。

      * `transform.position`:  **重要：**  这表示射线从挂载该脚本的 `GameObject` 的位置开始发射。这就是为什么你需要将这个脚本挂载到你的相机上（或者代表玩家视角的对象）。
      * `transform.forward`:  这表示射线朝着该 `GameObject` 的正前方发射。同样，这应该与玩家的视角方向一致。
      * `out hitInfo`:  如果射线击中了 `groundLayer` 上的物体，相关的信息（例如击中点、击中的物体等）将会存储在 `hitInfo` 变量中。
      * `maxDist`:  射线投射的最大距离。
      * `groundLayer`:  射线只会检测属于 `groundLayer` 的物体。
3. **确定目标方块:**

    * `Vector3 pointInTargetBlock;`:  计算射线击中点稍微偏移后的位置，以确保我们位于目标方块内部。
    * 根据是左键还是右键点击，`pointInTargetBlock` 会在击中点的基础上沿射线方向稍微偏移。
    * 接下来的代码将 `pointInTargetBlock` 的坐标转换为对应的地形块坐标 (`chunkPosX`, `chunkPosZ`) 和方块在块内的索引 (`bix`, `biz`, `biy`)。这里假设了地形块的宽度是 16。
    * `MyChunkPos cp = new MyChunkPos(chunkPosX, chunkPosZ);`:  创建一个 `MyChunkPos` 结构体来表示目标地形块的位置。
    * `MyTerrainChunk tc = MyTerrainGenerator.chunks[cp];`:  从 `MyTerrainGenerator` 的 `chunks` 字典中获取对应的 `MyTerrainChunk` 脚本实例。
4. **执行交互操作:**

    * **右键点击 (破坏方块):**

      * `inv.AddToInventory(tc.blocks[bix,biy,biz]);`:  将目标方块的类型添加到玩家的物品栏中。
      * `tc.blocks[bix, biy, biz] = MyBLockType.Air;`:  将目标方块的类型设置为空气，实现破坏的效果。
      * `tc.BuildMesh();`:  重新构建该地形块的网格，以显示破坏后的效果。
    * **左键点击 (放置方块):**

      * `if (inv.CanPlaceCur())`:  检查物品栏中当前选中的物品是否可以放置（数量大于 0）。
      * `tc.blocks[bix, biy, biz] = inv.GetCurBlock();`:  将目标方块的类型设置为物品栏中当前选中的方块类型。
      * `tc.BuildMesh();`:  重新构建该地形块的网格，以显示放置后的效果。
      * `inv.ReduceCur();`:  减少物品栏中当前选中物品的数量。

## 重要提示

* **挂载到相机：**  请务必将 `MyTerrainModifier1` 脚本挂载到你场景中代表玩家视角的相机 `GameObject` 上。这样，射线才能从玩家的视角正确地发射出去。
* **设置** **​`groundLayer`​**​ **：**  确保你在 Inspector 窗口中为 `groundLayer` 变量选择了包含你的地形的图层。
* **关联** **​`MyInventory`​**​ **：**  同样在 Inspector 窗口中，将你的 `MyInventory` 脚本实例拖拽到 `inv` 变量上。
* **调整** **​`maxDist`​**​ **：**  根据你的游戏需求调整 `maxDist` 的值，以控制玩家能够与地形交互的最大距离。

通过理解并正确配置 `MyTerrainModifier1` 脚本，你就可以让玩家与你创建的程序化地形进行互动，实现破坏和放置方块等功能，为你的游戏增添更多趣味性。