---
title: python uv github readme 介绍
published: 2024-08-28
tags: ['python']
description: python UV 新的包管理工具更加强大
image: https://cdn.jsdelivr.net/gh/Flandre923/CDN/img/1180dd77-64ee-11ef-8409-b81ea485754c.jpg
category: Python
draft: false
---


# python uv github readme 

[astral-sh/uv: An extremely fast Python package and project manager, written in Rust. (github.com)](https://github.com/astral-sh/uv?tab=readme-ov-file)

uv是一个用Rust编写的极速Python包和项目管理器。

* 它的速度极快，比pip快10到100倍。
* 可以安装和管理Python版本。
* 运行和安装Python应用程序。
* 支持运行单文件脚本，并支持内联依赖元数据。
* 提供全面的项目管理，包括通用锁文件。
* 包含一个与pip兼容的接口，提供熟悉的命令行界面，同时提升性能。
* 支持Cargo风格的工作空间，适用于可扩展的项目。
* 磁盘空间效率高，具有全局缓存，用于依赖项去重。
* 可以在不安装Rust或Python的情况下通过curl或pip安装。
* 支持macOS、Linux和Windows系统。

uv由Astral公司支持，也是Ruff的创建者。

# 安装

安装uv，你可以使用独立的安装程序，或从PyPI安装：

* macOS和Linux：

  ```bash
  curl -LsSf https://astral.sh/uv/install.sh | sh
  ```
* Windows：

  ```powershell
  powershell -c "irm https://astral.sh/uv/install.ps1 | iex"
  ```
* 使用pip：

  ```bash
  pip install uv
  ```

查看更多安装详情和替代安装方法，请查看[安装文档](https://kimi.moonshot.cn/chat/docs.astral.sh/uv)。

‍

‍

# 功能

* **项目管理**：uv管理项目依赖和环境，支持锁文件、工作空间等，类似于rye或poetry。
* **工具管理**：uv执行和安装由Python包提供的命令行工具，类似于pipx。
* **Python管理**：uv安装Python并允许快速切换版本。
* **脚本支持**：uv管理单文件脚本的依赖和环境。
* **与pip兼容的接口**：uv提供pip、pip-tools和virtualenv命令的替代品，同时增加高级功能。
* **平台支持**：查看uv的[平台支持文档](https://kimi.moonshot.cn/chat/cr6jepu0atp7d6rm0e0g#)。
* **版本政策**：查看uv的[版本政策文档](https://kimi.moonshot.cn/chat/cr6jepu0atp7d6rm0e0g#)。

‍

# 项目管理

uv管理项目依赖和环境，支持锁文件、工作空间等功能，类似于rye或poetry：

```bash
$ uv init example
```

在`/home/user/example`​初始化了项目`example`​。

```bash
$ cd example
```

```bash
$ uv add ruff
```

在`.venv`​创建了虚拟环境。 

```bash
$ uv run ruff check
```

# 工具管理

uv执行并安装由Python包提供命令行工具，类似于pipx。

使用uvx（uv tool run的别名）在临时环境中运行工具：

```bash
$ uvx pycowsay 'hello world!'
```

显示输出：

```
  ------------
  < hello world! >
  ------------
    \  ^__^
     \ (oo)\_______
        (__)\       )\/\
            ||----w |
            ||   
```

使用uv tool install安装工具：

```bash
$ uv tool install ruff
```

```bash
$ ruff --version
```

ruff 0.5.4 查看工具文档开始使用。

# python 管理

uv安装Python并允许快速切换版本。

安装多个Python版本：

```bash
$ uv python install 3.10 3.11 3.12
```

按需下载Python版本：

```python
$ uv venv --python 3.12.0
Using Python 3.12.0
Creating virtualenv at: .venv
Activate with: source .venv/bin/activate

$ uv run --python pypy@3.8 -- python --version
Python 3.8.16 (a9dbdca6fc3286b0addd2240f11d97d8e8de187a, Dec 29 2022, 11:45:30)
[PyPy 7.3.11 with GCC Apple LLVM 13.1.6 (clang-1316.0.21.2.5)] on darwin
Type "help", "copyright", "credits" or "license" for more information.
>>>>
```

# 脚本支持

uv管理单文件脚本的依赖和环境。

创建一个新的脚本，并添加内联元数据声明其依赖：

```python
$ echo 'import requests; print(requests.get("https://astral.sh"))' > example.py

$ uv add --script example.py requests
Updated `example.py`
```

然后，在隔离的虚拟环境中运行脚本：

```python
$ uv run example.py
Reading inline script metadata from: example.py
Installed 5 packages in 12ms
<Response [200]>
```

# 与pip的兼容

uv提供了一个可替代常见pip、pip-tools和virtualenv命令的接口。

uv通过高级功能扩展了它们的接口，例如依赖版本覆盖、平台无关的解析、可复现的解析、替代的解析策略等。

无需改变现有的工作流程，就可以迁移到uv——并通过uv pip接口体验10-100倍的速度提升。

将需求编译到一个平台无关的需求文件中：

```python
$ uv pip compile docs/requirements.in \
   --universal \
   --output-file docs/requirements.txt
Resolved 43 packages in 12ms
```

创建一个虚拟环境

```python
$ uv venv
Using Python 3.12.3
Creating virtualenv at: .venv
Activate with: source .venv/bin/activate
```

安装锁定的需求：

```python
$ uv pip sync docs/requirements.txt
Resolved 43 packages in 11ms
Installed 43 packages in 208ms
 + babel==2.15.0
 + black==24.4.2
 + certifi==2024.7.4
 ...
```