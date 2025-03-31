---
title: Untity学习记录-实现一个完整的2D游戏Demo-36-打包和生成游戏
published: 2025-03-31
tags: ['Unity','游戏','demo','2d']
description: 使用Untiy时间一个2D冒险游戏demo
image: https://cdn.jsdelivr.net/gh/Flandre923/CDN@latest/img/20250331195820.png
category: Unity
draft: false
---
# 从编辑器到可执行文件：Unity游戏打包终极指南

在这个系列的最终章（或者说，如果你是直接跳到这里的，这是一个关于打包的全面指南！），我们将一起梳理打包前的准备工作，解决一些常见“陷阱”，并配置好各种设置，最终生成你的游戏！

## 为什么我的游戏“打不了包”？—— Build Settings 与 Addressables 的小秘密

很多时候，尤其是在使用了 Addressables（可寻址资产系统）来管理资源的项目中，当你兴冲冲地打开 `File > Build Settings...` 准备打包时，Unity 可能会给你泼一盆冷水，弹出一个错误，告诉你无法构建。

**原因是什么呢？**

问题出在 `Scenes In Build` 列表。Unity 需要知道至少要包含哪个场景作为启动入口。但在我们之前的开发流程中，所有的游戏场景（比如主菜单、关卡地图等）都是通过 Addressables 动态加载的，并没有直接添加到这个列表里。这就导致 Unity 认为“嗯？你好像没给我任何需要打包的场景啊？那我怎么开工？”


## 解决方案：创建“初始化”场景

为了解决这个问题，并顺便让我们的初始包体尽可能小，我们需要创建一个专门用于启动和初始化的“迷你”场景。

1. **创建新场景**: 在你的 `Assets/Scenes` 文件夹（或者你组织场景的地方）右键 `Create > Scene`，命名为 `Initialization`。
2. **保持极简**: 双击打开这个新场景。它应该是非常干净的，**删除**默认创建的 `Main Camera` 和 `Directional Light`（如果它们存在的话）。我们只需要一个空的游戏对象（GameObject）。
3. **创建加载器**: 在场景层级（Hierarchy）中创建一个空的 GameObject (`Create > Create Empty`)，并将其命名为 `InitialLoad`。
4. **编写加载脚本**:

    * 在你的 `Scripts` 文件夹下（比如 `Scripts/Transition` 或你觉得合适的地方），创建一个新的 C# 脚本，命名为 `InitialLoad`。
    * 将这个脚本附加到场景中的 `InitialLoad` GameObject 上。
    * 双击打开脚本，编写以下代码：

    ```csharp
    using UnityEngine;
    using UnityEngine.AddressableAssets; // 引入 Addressables 命名空间
    using UnityEngine.ResourceManagement.AsyncOperations; // 可能需要，用于操作句柄
    using UnityEngine.ResourceManagement.ResourceProviders; // 可能需要，用于场景实例
    using UnityEngine.SceneManagement; // 如果你需要场景加载模式等

    public class InitialLoad : MonoBehaviour
    {
        // 使用 AssetReference 指向你的持久化场景（或其他第一个要加载的场景）
        public AssetReference persistentSceneReference;

        async void Start() // 使用 async Start 更方便处理异步加载
        {
            if (persistentSceneReference == null || !persistentSceneReference.RuntimeKeyIsValid())
            {
                Debug.LogError("Persistent Scene Reference is not set or invalid!");
                return;
            }

            try
            {
                // 异步加载持久化场景
                // LoadSceneAsync 会加载场景并激活它（如果只有一个场景）
                // 或者你可以选择 LoadSceneMode.Additive 如果需要叠加加载
                // 注意：Addressables 加载场景返回的是 SceneInstance，不是传统的 Scene 对象
                AsyncOperationHandle<SceneInstance> handle = Addressables.LoadSceneAsync(persistentSceneReference, LoadSceneMode.Additive); // 或者 Single

                // 等待场景加载完成
                await handle.Task;

                if (handle.Status == AsyncOperationStatus.Succeeded)
                {
                    Debug.Log("Persistent scene loaded successfully via Addressables.");
                    // 可以在这里执行场景加载后的初始化逻辑，
                    // 或者让 Persistent 场景自己处理初始化 (更推荐)

                    // 可选：加载完成后，可以卸载当前这个极简的 Initialization 场景
                    // SceneManager.UnloadSceneAsync(gameObject.scene); // 小心使用，确保逻辑正确
                }
                else
                {
                    Debug.LogError($"Failed to load persistent scene: {handle.OperationException}");
                }
            }
            catch (System.Exception e)
            {
                Debug.LogError($"Exception during scene loading: {e}");
            }
        }
    }
    ```
5. **配置脚本**: 返回 Unity 编辑器，选中 `InitialLoad` GameObject。在 Inspector 面板中，找到 `Initial Load (Script)` 组件。你会看到 `Persistent Scene Reference` 字段。点击它旁边的小圆圈按钮或直接拖拽，将你的**持久化场景（Persistent Scene）**的 Addressable 资产（通常在 Addressables Groups 窗口里能看到）指定给它。

6. **添加到 Build Settings**: 现在，打开 `File > Build Settings...`。将场景 `Initialization` 从 Project 窗口拖拽到 `Scenes In Build` 列表中。确保它是列表中的**第一个**（索引为 0）并且是**唯一**勾选的场景（除非你有特殊需求）。如果你之前有其他场景（比如测试用的），记得将它们**移除**或**取消勾选**。


好了！现在 Unity 知道从哪里开始了。它会先加载这个超小的 `Initialization` 场景，然后这个场景里的脚本会通过 Addressables 加载你的真正游戏内容。

## Addressables 打包模式设置

在我们最终点击 "Build" 按钮之前，还需要确保 Addressables 本身也准备好了发布。

1. **打开 Addressables Groups 窗口**: `Window > Asset Management > Addressables > Groups`。
2. **选择 Play Mode Script**: 在窗口顶部附近，找到 `Play Mode Script`（播放模式脚本）下拉菜单。

    * `Use Asset Database (faster)`: 这是你在编辑器中快速迭代和测试时使用的模式。它直接从你的项目 `Assets` 文件夹加载资源，速度快，但**不适用于**真实构建。
    * `Simulate Groups (advanced)`: 模拟打包后的加载，但仍不构建内容。
    * `Use Existing Build (requires built groups)`: **这才是我们打包发布时需要选择的模式**。它意味着游戏运行时会去查找和加载你**已经构建好**的 Addressables 内容包（Asset Bundles）。
3. **构建 Addressables 内容**: 选择了 `Use Existing Build` 后，你有两种方式来构建 Addressables 内容：

    * **手动构建**: 在 Addressables Groups 窗口，点击顶部的 `Build > New Build > Default Build Script`。Unity 会处理所有的 Addressable 资源，并将它们打包成 Asset Bundles，存放在 `Library/com.unity.addressables/aa/[Platform]` 目录下（或者你自定义的输出目录）。**每次修改了 Addressable 资源或设置后，理论上都需要重新构建一次**。
    * **自动构建**: 更方便的方法是让 Unity 在每次构建播放器时自动帮你构建 Addressables。

      * 在 Project 窗口找到 `Assets/AddressableAssetsData/AddressableAssetSettings.asset`。
      * 选中它，在 Inspector 窗口中找到 `Build & Play Mode Scripting` 部分（可能需要展开）。
      * 勾选 `Build Addressables on Player Build`（在 Player 构建时构建 Addressables）选项。下拉菜单可以选择构建哪个脚本，通常是 `Default Build Script`。

      这样，每次你点击 `File > Build Settings... > Build` 时，Unity 会先自动执行 Addressables 的构建流程，然后再构建你的游戏。

**我推荐使用自动构建的方式**，这样不容易忘记构建 Addressables 导致运行时找不到资源。但如果你想更精细地控制，或者构建时间较长想分开进行，手动构建也是可以的。

*实践环节：* 在这里，无论你选择哪种方式，**建议先手动执行一次 `Build > New Build > Default Build Script`**，确保 Addressables 内容至少成功构建过一次。关闭弹出的构建报告即可。

## 解决恼人的缝隙：Sprite Atlas 来帮忙

你可能在开发过程中（或者看其他教程时）遇到过，Tilemap 瓦片之间或者拼接的 Sprite 之间偶尔会出现细微的缝隙，尤其是在摄像机移动或缩放时。

![示意图：Tilemap 缝隙问题](https://via.placeholder.com/400x300.png?text=Visual+Seam/Gap+in+Tiles)

这个问题通常是由于浮点数精度、纹理滤波或 Mipmap 等原因造成的。一个非常有效的解决方案是使用 **Sprite Atlas**（精灵图集）。

**Sprite Atlas 的作用**:

* 将多个小的 Sprite 打包到一个或多个大的纹理（图集）中。
* 减少 Draw Call（绘制调用），提升渲染性能。
* **通过控制打包过程中的填充（Padding），有效解决精灵间的缝隙问题。**
* 可能有助于优化内存占用和加载时间。

**如何使用**:

1. **创建 Sprite Atlas**: 在 Project 窗口中你管理美术资源的地方（比如 `Assets/Art/Sprites`），右键 `Create > 2D > Sprite Atlas`。给它一个有意义的名字，比如 `GameplayAtlas` 或 `TilemapAtlas`。
2. **添加对象**: 选中你创建的 Sprite Atlas 资产。在 Inspector 窗口中，你会看到一个 `Objects for Packing`（用于打包的对象）列表。

    * 将你游戏中实际用到的**所有**相关 Sprite 文件或**包含 Sprite 的文件夹**（比如你的 Tilemap 瓦片文件夹、角色动画序列帧文件夹等）拖拽到这个列表里。
3. **预览和打包**: 点击 Inspector 窗口底部的 `Pack Preview` 按钮。Unity 会计算并将这些 Sprite 排列到预览纹理上。
4. **重要设置 (针对像素风格游戏)**:

    * **Filter Mode**: 如果你的游戏是像素风格，默认的 `Bilinear` 滤波会让图像模糊。将其改为 `Point (no filter)`。
    * **Compression**: 为了保持像素的锐利，将压缩设置为 `None`。
    * **Max Texture Size**: 根据你的资源量和目标平台调整。如果 Unity 提示你的图集太大无法容纳所有 Sprite，你可能需要增大这个值（比如 2048x2048 或 4096x4096）。注意，更大的纹理会占用更多内存。如果一个 Atlas 实在放不下，可以考虑创建多个 Atlas 按类别（如 UI、角色、环境）分开打包。


    *注意*: 设置为 `Point (no filter)` 和 `None` 压缩会得到最清晰的像素效果，但同时会**显著增加**图集的最终文件大小和内存占用。你需要在**视觉效果**和**性能/包体大小**之间做出权衡。对于非像素风格的游戏，可以使用默认设置或适当的压缩。
5. **保存**: 对 Sprite Atlas 的修改会自动保存。现在，当 Unity 渲染这些 Sprite 时，会从打包好的图集中采样，缝隙问题通常就能得到解决。别忘了**保存你的项目** (`File > Save Project`)。

## 最后的冲刺：Player Settings 配置

在点击最终的 "Build" 按钮前，我们还需要对游戏本身做一些配置。回到 `File > Build Settings...`，点击左下角的 `Player Settings...` 按钮，会打开 Project Settings 窗口并定位到 Player 选项卡。