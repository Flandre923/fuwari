---
title: JetBrains Rider 免费了！Unity 开发者的终极代码编辑器体验
published: 2025-04-01
tags: ['Unity','IDE','Rider']
description: 使用Untiy时间一个2D冒险游戏demo
image: ./image.png
category: Unity
draft: false
---


业界公认的最佳 Unity 开发代码编辑器 Rider，现在对非商业用途用户免费开放了！

## 🎉 Rider 为何成为 Unity 开发者的首选？

Rider 一直被 Unity 开发者誉为"终极代码编辑器"，它专为 Unity 开发优化，提供了远超 Visual Studio 和 VS Code 的智能提示、代码分析和调试功能。现在免费了，简直是独立开发者和学生党的福音！

## 💻 下载与安装 Rider

1. 访问 [JetBrains Rider 官网](https://www.jetbrains.com/rider/)
2. 点击下载（支持 Windows、macOS 和 Linux）
    * **Apple Silicon 用户注意**：在下拉菜单中选择 Apple Silicon 版本
3. 安装过程：

    * **macOS**：拖拽到 Applications 文件夹
    * **Windows**：运行安装程序按向导操作

## ⚙️ 初始配置技巧

首次启动 Rider 时，我推荐以下设置：

1. **主题选择**：Rider 自带主题已经很漂亮，但如果你习惯 VS，可以选择 VS 主题
2. **快捷键映射**：支持 VS Code、Visual Studio 等常用快捷键方案
3. **中文界面**（如需）：

    * 进入 Settings → System Settings → Language & Region
    * 选择"简体中文"并保存

## 🔗 Unity 项目配置 Rider 为默认编辑器

1. 在 Unity 中打开 Edit → Preferences → External Tools
2. 如果 Rider 未出现在下拉列表中：

    * 打开 Package Manager
    * 搜索并安装 "JetBrains Rider Editor" 包
3. 返回 External Tools，选择 Rider 作为默认代码编辑器

## 🛠️ Rider 个性化设置

### 1. 字体与界面调整

```csharp
Settings → Editor → Font
```

* 推荐字体大小 16-18（可根据屏幕尺寸调整）
* 使用 `Cmd/Ctrl + +/-` 快速调整编辑器字体大小

### 2. 移除强制换行参考线

```csharp
Settings → Editor → General → Appearance
```

* 取消勾选"Show hard wrap and visual guides"

### 3. 代码提示级别控制

编辑器右下角有三个小画笔图标，可以设置：

* **语法**：仅基本语法检查
* **提示**：改进建议（推荐）
* **建议**：更多优化建议
* **警告**：潜在问题
* **错误**：仅显示错误

## 💡 Rider 的杀手级功能

### 1. 智能代码分析与重构

* 变量名下的波浪线提示改进建议
* 点击"灯泡"图标快速应用重构
* 示例：将普通字段转为 `readonly`，`for` 循环转为 `foreach`

### 2. 强大的 Unity 集成

* **资源追踪**：点击类名上的"资源用法"查看场景中的使用情况
* **序列化字段同步**：直接在编辑器修改的字段值会实时反映在代码提示中
* **ScriptableObject 管理**：快速定位 SO 资源使用位置

### 3. 增强版 TODO 系统

```csharp
Settings → Editor → TODO
```

* 支持自定义标签（如 BUG、TEST）和颜色
* 无需插件，原生支持代码注释标记

### 4. 无与伦比的调试体验

* 无缝 Unity 调试集成
* 变量值实时查看
* 条件断点支持
* Unity Console 日志深度集成

### 5. 快速创建 Unity 专用文件

* 右键菜单直接创建：

  * ScriptableObject
  * Shader 文件
  * 各种 Unity 脚本模板

## 🎮 Shader 开发利器

Rider 提供了市面上最好的 Shader 开发支持：

* 智能代码补全
* 语法高亮
* 代码格式化
* 错误检测

## 🚀 调试实战演示
1. 在代码中设置断点
2. 点击 Rider 右上角的"虫子"图标启动调试
3. 在 Unity 中运行游戏
4. 断点命中后：

    * 查看变量值
    * 单步执行
    * 修改运行状态

## 🌟 为什么选择 Rider？

1. **智能提示**：远超其他编辑器的代码理解能力
2. **Unity 深度集成**：真正为 Unity 开发者打造
3. **性能优越**：大型项目依然流畅
4. **全平台支持**：无论 Windows、macOS 还是 Linux
5. **现在免费了**！非商业用途无需付费