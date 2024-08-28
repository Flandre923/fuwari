---
title: Ruff 一个现代的Python代码检查工具，用于无错误且易于维护的代码。
published: 2024-08-28
tags: ['python']
description: python ruff 强大的代码检查和格式化工具
image: https://cdn.jsdelivr.net/gh/Flandre923/CDN/img/1306dc81-64ee-11ef-8b4c-b81ea485754c.jpg
category: Python
draft: false
---


# Ruff: 一个现代的Python代码检查工具，用于无错误且易于维护的代码。

# [Ruff: A Modern Python Linter for Error-Free and Maintainable Code – Real Python](https://realpython.com/ruff-python/)

# 安装

pip 安装

​`$ python -m pip install ruff`​

macOS

​`$ brew install ruff`​

conda 用户安装

```
$ conda install -c conda-forge ruff
```

此外，如果你希望 Ruff 对所有项目都可用，你可能想要通过 pipx 来安装 Ruff。

你可以通过使用 `ruff version`​ 命令来检查 Ruff 是否正确安装。

```python
$ ruff version
ruff 0.4.7
```

为了让 `ruff`​ 命令在你的 PATH 中显示，你可能需要关闭并重新打开你的终端应用程序，或者启动一个新的终端会话。

# 代码检查您的Python代码

下面是一个简单的脚本。包含了一个错误，现在使用ruff检测是否包含了错误。

```python
import os
import random

CHARACTERS = ("Frodo", "Sam", "Merry", "Pippin", "Aragorn", "Legolas", "Gimli", "Boromir", "Gandalf", "Saruman", "Sauron")

def random_character():
    return random.choice(CHARACTERS)

def ring_bearer():
    return name in ("Frodo", "Sam")

if __name__ == "__main__":
    character = random_character()
    if ring_bearer(character):
        print(f"{character} is a ring bearer")
    else:
        print(f"{character} is not a ring bearer")
```

Ruff 命令行界面（CLI）最基本的命令是 `check`​。默认情况下，这个命令会检查当前目录下的所有文件。

```python
$ ruff check
one_ring.py:1:8: F401 [*] `os` imported but unused
one_ring.py:10:12: F821 Undefined name `name`
Found 2 errors.
[*] 1 fixable with the `--fix` option.
```

成功！Ruff 发现了两个错误。它不仅显示了错误的文件和行号，还提供了错误代码和消息。此外，它还让你知道这两个错误中的一个是可修复的。太好了！

如果你有更多的文件，你可以使用 `ruff check one_ring.py`​ 来检查单个文件。而且，如果你喜欢将文件保存在 `src/`​ 目录下并且有多个嵌套目录，那么 `ruff check src/`​ 将检查 `src/`​ 文件夹中的所有文件和子目录。

你可以告诉 Ruff 通过应用 `--fix`​ 标志来修复错误。

```python
$ ruff check --fix
one_ring.py:9:12: F821 Undefined name `name`
Found 2 errors (1 fixed, 1 remaining).
```

一个导入的包未被使用被移除了，另一个被保留了。

由于 Ruff 提供了错误代码，你可以将它传递给 `ruff rule`​ 命令，以查看有关错误消息的更多详细信息，包括代码示例：

```python
$ ruff rule F821
```

当你运行这个命令时，你会在终端中以 Markdown 格式获得更多详细信息：

```python
# undefined-name (F821)

Derived from the **PyFlakes** linter.

## What it does
Checks for uses of undefined names.

## Why is this bad?
An undefined name is likely to raise `NameError` at runtime.

## Example

‍```python
def double():
    return n * 2  # raises `NameError` if `n` is undefined when `double` is called
‍```

Use instead:

‍```python
def double(n):
    return n * 2
‍```

## References
- [Python documentation: Naming and binding](https://docs.python.org/3/reference/executionmodel.html#naming-and-binding)
```

所以给方法加上参数name就可以了。

再次运行

```python
$ ruff check
All checks passed!
```

# 加快您的工作流程

```
$ ruff check --watch
```

ruff将会监听你的文件的变动

```python
[14:04:01 PM] Starting linter in watch mode...
[14:04:01 PM] Found 0 errors. Watching for file changes.
```

# 发现更多的错误

默认情况下，Ruff 启用了 Flake8 的 F 规则以及 E 规则的一个子集，同时省略了与使用格式化工具（如 ruff format 或 Black）重叠的任何风格规则。

Ruff 出厂设置并不应用检查行长度的规则。然而，你可以告诉它你想要包括或排除哪些额外的规则。你可以要求它包括所有的 E 规则或使用 --select 标志指定一个特定的规则：

```python
$ ruff check --select E
one_ring.py:4:89: E501 Line too long (122 > 88)
Found 1 error.

$ ruff check --select E501
one_ring.py:4:89: E501 Line too long (122 > 88)
Found 1 error.
```

对于这类的格式化问题可以有其他的方式进行修复。ruff format

# 格式化你的代码

```python
$ ruff format
1 file reformatted
```

随后你的代码看起来是这个样子：

```python
import random

CHARACTERS = (
    "Frodo",
    "Sam",
    "Merry",
    "Pippin",
    "Aragorn",
    "Legolas",
    "Gimli",
    "Boromir",
    "Gandalf",
    "Saruman",
    "Sauron",
)


def random_character():
    return random.choice(CHARACTERS)


def ring_bearer(name):
    return name in ("Frodo", "Sam")


if __name__ == "__main__":
    character = random_character()
    if ring_bearer(character):
        print(f"{character} is a ring bearer")
    else:
        print(f"{character} is not a ring bearer")
```

如果你想在运行 ruff format 时看到将做出哪些更改，你可以使用 --diff 标志来在进行更改之前查看建议的更改。如果你在运行 ruff format 之前运行了 --diff 标志，你会看到这样的输出：

```python
--- one_ring.py
+++ one_ring.py
@@ -1,16 +1,31 @@
 import random


-CHARACTERS = ("Frodo", "Sam", "Merry", "Pippin", "Aragorn", "Legolas", "Gimli", "Boromir", "Gandalf", "Saruman", "Sauron")
+CHARACTERS = (
+    "Frodo",
+    "Sam",
+    "Merry",
+    "Pippin",
+    "Aragorn",
+    "Legolas",
+    "Gimli",
+    "Boromir",
+    "Gandalf",
+    "Saruman",
+    "Sauron",
+)
+

 def random_character():
     return random.choice(CHARACTERS)

+
 def ring_bearer(name):
     return name in ("Frodo", "Sam")

+
 if __name__ == "__main__":
     character = random_character()
     if ring_bearer(character):
         print(f"{character} is a ring bearer")
     else:
-        print(f"{character} is not a ring bearer")
\ No newline at end of file
+        print(f"{character} is not a ring bearer")

1 file would be reformatted
```

# 配置 ruff

Ruff 允许您将配置存储在 TOML 文件中。更具体地说，可以是 ruff.toml、.ruff.toml，或者您现有的 pyproject.toml 文件。

ruff.toml

```toml
line-length = 88

[lint]
select = ["E501", "I"]

[format]
docstring-code-format = true
docstring-code-line-length = 72
```

以下是 pyproject.toml 格式的相同示例。唯一的变化是您需要在每个表格标题中包含一个 tool.ruff 前缀：

```toml
[tool.ruff]
line-length = 88 //你已经指定在用 ruff 进行检查时要包含 E501 规则，当行长度超过默认的 88 个字符时会返回错误。

[tool.ruff.lint]
select = ["E501", "I"] // 除了在检查配置中添加 E501 规则外，你还要求 Ruff 添加所有的 I 规则。

[tool.ruff.format]
docstring-code-format = true
docstring-code-line-length = 72 //将你的文档字符串格式化为 72 个字符的长度。
```

# vscode集成

​![image](https://cdn.jsdelivr.net/gh/Flandre923/CDN/img/140fc2ea-64ee-11ef-ac73-b81ea485754c.png)​

安装即可

快速修复

​![image](https://cdn.jsdelivr.net/gh/Flandre923/CDN/img/14a17901-64ee-11ef-8cd4-b81ea485754c.png)​

全部修复

​![image](https://cdn.jsdelivr.net/gh/Flandre923/CDN/img/154d21ab-64ee-11ef-bdeb-b81ea485754c.png)​

格式化文档

​![image](https://cdn.jsdelivr.net/gh/Flandre923/CDN/img/15e1b291-64ee-11ef-9543-b81ea485754c.png)​

优化导入

​![image](https://cdn.jsdelivr.net/gh/Flandre923/CDN/img/16807671-64ee-11ef-a13a-b81ea485754c.png)​

## 用法

用法 一旦在 Visual Studio Code 中安装，当你打开或编辑 Python 或 Jupyter Notebook 文件时，ruff 将自动执行。

如果你想禁用 Ruff，你可以在 Visual Studio Code 中每个工作区禁用此扩展。