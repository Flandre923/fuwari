---
title: python uv 包管理工具
published: 2024-08-28
tags: ['python']
description: python UV 新的包管理工具更加强大
image: https://cdn.jsdelivr.net/gh/Flandre923/CDN/img/0f45c123-64ee-11ef-946a-b81ea485754c.jpg
category: Python
draft: false
---


# python uv 包管理工具

python uv github readme

# 安装

```cmd
# On macOS and Linux.
$ curl -LsSf https://astral.sh/uv/install.sh | sh

# On Windows.
$ powershell -c "irm https://astral.sh/uv/install.ps1 | iex"

# With pip.
$ pip install uv
```

需要你开启脚本执行

```powershell
Set-ExecutionPolicy RemoteSigned -scope CurrentUser
```

安装位置是：

```toml
Downloading uv 0.3.4 (x86_64-pc-windows-msvc)
Installing to C:\Users\flan\.cargo\bin
  uv.exe
  uvx.exe
everything's installed!

C:\Users\flan\.cargo\bin was added to your PATH, you may need to restart your shell for that to take effect.
```

重启的你的shell 就可以使用了

‍

卸载

```bash

rm ~/.cargo/bin/uv ~/.cargo/bin/uvx

```

# uv feature

uv是一个为Python开发提供关键特性的工具，它支持从安装Python环境、编写简单脚本到处理支持多版本和多平台的大型项目。以下是uv的一些主要功能：

## Python versions

* ​`uv python install`​：安装Python版本。这允许你安装所需的Python版本，即使系统中已存在Python。
* ​`uv python list`​：列出所有可用的Python版本，包括已安装和可安装的版本。
* ​`uv python find`​：查找系统中已安装的Python版本。
* ​`uv python pin`​：将当前项目固定使用特定的Python版本，确保项目开发环境的一致性。
* ​`uv python uninstall`​：卸载不再需要的Python版本。

## Scripts

执行一个单独的脚本，例如 exmaple.py

1. uv run: 运行一个脚本
2. uv add --script: 给脚本添加一个依赖
3. uv remove --script: 给脚本移除一个依赖

## project

创建和使用Python项目的工作方式，即涉及`pyproject.toml`​文件。

* ​`uv init`​：创建一个新的Python项目。
* ​`uv add`​：向项目添加依赖项。
* ​`uv remove`​：从项目中移除依赖项。
* ​`uv sync`​：同步项目依赖项与环境。
* ​`uv lock`​：为项目的依赖项创建锁文件。
* ​`uv run`​：在项目环境中运行命令。
* ​`uv tree`​：查看项目的依赖树。

## tools

运行和安装发布到Python包索引的工具，例如ruff或black。

* ​`uvx`​ / `uv tool run`​：在临时环境中运行工具。
* ​`uv tool install`​：全局安装工具。
* ​`uv tool uninstall`​：卸载工具。
* ​`uv tool list`​：列出已安装的工具。
* ​`uv tool update-shell`​：更新shell以包含工具的可执行文件。

## pip 接口

手动管理环境和包 — 旨在用于遗留工作流程或高级命令无法提供足够控制的情况。

创建虚拟环境（替代venv和virtualenv）：

* ​`uv venv`​：创建一个新的虚拟环境。 查看使用环境的文档了解详细信息。

在环境中管理包（替代pip和pipdeptree）：

* ​`uv pip install`​：将包安装到当前环境。
* ​`uv pip show`​：显示已安装包的详细信息。
* ​`uv pip freeze`​：列出已安装的包及其版本。
* ​`uv pip check`​：检查当前环境是否有兼容的包。
* ​`uv pip list`​：列出已安装的包。
* ​`uv pip uninstall`​：卸载包。
* ​`uv pip tree`​：查看环境的依赖树。 查看管理包的文档了解详细信息。

锁定环境中的包（替代pip-tools）：

* ​`uv pip compile`​：将需求编译成锁文件。
* ​`uv pip sync`​：将环境与锁文件同步。

# 实用工具

实用工具# 管理和检查uv的状态，例如缓存、存储目录，或执行自我更新：

* ​`uv cache clean`​：清除缓存条目。
* ​`uv cache prune`​：清除过时的缓存条目。
* ​`uv cache dir`​：显示uv缓存目录路径。
* ​`uv tool dir`​：显示uv工具目录路径。
* ​`uv python dir`​：显示uv安装的Python版本路径。
* ​`uv self update`​：将uv更新到最新版本。

‍

# 使用uv安装python

1. **自动检测Python**：  
    如果系统中已经安装了Python，uv会检测到并使用它，无需额外配置。

2. **自动获取Python版本**：  
    uv会在需要时自动获取Python版本，你无需预先安装Python即可开始使用uv。
3. **安装最新Python版本**：  
     要安装最新版本的Python，可以使用以下命令

    ```toml
    uv python install
    ```

    这会安装一个由uv管理的Python版本，即使系统中已经存在Python安装。
4. **使用第三方Python发行版**： 由于Python官方不发布独立的二进制发行版，uv使用来自`python-build-standalone`​项目的第三方发行版。这个项目部分由uv维护者维护，并被其他著名的Python项目使用。
5. **安装特定版本的Python**： 要安装特定版本的Python，可以使用：

    ```bash
    uv python install 3.12
    ```

    安装多个版本：

    ```bash
    uv python install 3.11 3.12
    ```

    安装替代Python实现，如PyPy：

    ```toml
    uv python install pypy@3.16
    ```

6. **查看Python安装情况**： 要查看可用和已安装的Python版本，可以使用：

    ```bash
    uv python list
    ```
7. **使用现有Python安装**： uv会使用系统中现有的Python安装（如果存在）。要强制uv使用系统Python，可以使用`--python-preference only-system`​选项。
8. **Python版本概念页面和命令参考**： 要了解更多关于uv Python的信息，可以查看Python版本概念页面和命令参考。
9. **运行脚本和使用uv调用Python**： 阅读更多相关内容以了解如何使用uv运行脚本和调用Python。

请注意，当uv安装Python时，它不会全局可用（即通过`python`​命令）。计划在未来版本中支持此功能。与此同时，可以使用`uv run`​或创建并激活虚拟环境来直接使用Python。如果你有任何问题或需要进一步的帮助，请随时提问。

‍

# 运行脚本

Python脚本是一个设计为独立执行的文件，例如，使用`python example.py`​。使用uv来执行脚本可以确保脚本的依赖项得到管理，而无需手动管理环境。

## 运行脚本没有额外依赖

example.py

```python
print("Hello world")
```

```python
uv run example.py
```

如果是依赖于标准库也是一样的

example.py

```
import os

print(os.path.expanduser("~"))
```

```
$ uv run example.py
/Users/astral
```

提供参数

example.py

```
import sys

print(" ".join(sys.argv[1:]))
```

```
$ uv run example.py test
test

$ uv run example.py hello world!
hello world!
```

注意，如果你在包含`pyproject.toml`​文件的项目目录中使用`uv run`​命令，uv会先安装当前项目，然后再运行脚本。如果你的脚本不依赖于项目，请使用`--no-project`​标志来跳过这一步：

```python
uv run --no-project example.py
```

## 运行带有依赖的脚本

当您的脚本需要其他包时，这些包必须安装在脚本运行的环境中。uv倾向于按需创建这些环境，而不是使用长期存在的虚拟环境并手动管理依赖项。这要求明确声明脚本所需的依赖项。通常建议使用项目或内联元数据来声明依赖项，但uv也支持每次调用时请求依赖项。

例如，以下脚本需要`rich`​库。

```python
import time
from rich.progress import track

for i in track(range(20), description="For example:"):
    time.sleep(0.05)
```

没有依赖取执行该脚本会报错

```python
uv run --no-project example.py
```

通过--with 选项设置

```python
uv run --with rich example.py
```

如果需要特定版本的包，可以对请求的依赖项添加约束：

```bash
uv run --with 'rich>12,<13' example.py
```

可以通过重复使用`--with`​选项来请求多个依赖项。

注意，如果在项目中使用`uv run`​，这些依赖项将作为项目的依赖项的补充被包含在内。要退出这种行为，使用`--no-project`​标志。

## 声明脚本依赖

Python最近增加了一个用于内联脚本元数据的标准格式。这允许在脚本本身中声明脚本的依赖项。

uv支持为您添加和更新内联脚本元数据。使用`uv add --script`​来声明脚本的依赖项：

```
uv add --script example.py 'requests<3' 'rich'
```

这将在脚本顶部添加一个脚本部分，使用TOML格式声明依赖项：

```python
# /// script
# dependencies = [
#   "requests<3",
#   "rich",
# ]
# ///

import requests
from rich.pretty import pprint

resp = requests.get("https://peps.python.org/api/peps.json")
data = resp.json()
pprint([(k, v["title"]) for k, v in data.items()][:10])
```

uv将自动创建一个包含运行脚本所需的依赖项的环境，例如：

```python

uv run example.py
```

当使用内联脚本元数据时，即使在项目中使用`uv run`​，项目中的依赖项也会被忽略。不需要`--no-project`​标志。

## 使用不同的版本的python运行脚本

uv允许在每次脚本调用时请求任意Python版本，例如：

​`uv run --python 3.10 example.py3.10.13`​

## GUI脚本

在Windows上，uv将使用`pythonw`​来运行以`.pyw`​扩展名结尾的脚本：

example.pyw

```python
from tkinter import Tk, ttk

root = Tk()
root.title("uv")
frm = ttk.Frame(root, padding=10)
frm.grid()
ttk.Label(frm, text="Hello World").grid(column=0, row=0)
root.mainloop()
```

```python
PS> uv run example.pyw

```

与依赖类似

example\_pyqt.pyw

```python
import sys
from PyQt5.QtWidgets import QApplication, QWidget, QLabel, QGridLayout

app = QApplication(sys.argv)
widget = QWidget()
grid = QGridLayout()

text_label = QLabel()
text_label.setText("Hello World!")
grid.addWidget(text_label)

widget.setLayout(grid)
widget.setGeometry(100, 100, 200, 50)
widget.setWindowTitle("uv")
widget.show()
sys.exit(app.exec_())
```

PS> uv run --with PyQt5 example_pyqt.pyw

‍

# 使用tools

许多Python包提供了可以作为工具使用的应用程序。uv有专门支持，可以方便地调用和安装工具。

## 运行工具

​`uvx`​命令可以在不安装工具的情况下调用它。

例如，要运行`ruff`​：

```python
uvx ruff
```

与此等价

```python
uv tool run ruff
```

参数可以在工具名称之后提供

```python
uvx pycowsay hello from uv

```

使用`uvx`​时，工具会被安装在临时的、隔离的环境中。

## 处理名命名和包名不一致的情况

当使用`uvx`​命令调用工具时，可能会遇到包名和命令名不一致的情况。以下是如何使用uv来处理这种情况的讲解：

当包名和命令名不一致时，可以使用`--from`​选项来指定要从哪个包调用命令。例如，要调用由`httpie`​包提供的`http`​命令，你应该使用以下命令：

​`uvx --from httpie http`​

​`--from`​选项告诉`uv`​你想要调用的命令来自特定的包。这样，`uv`​就能够正确地找到并安装包含该命令的包，然后执行它

## 请求特定的版本

**使用**​**​`command@<version>`​** ​**运行特定版本**：

​`uvx ruff@0.3.0 check`​

**使用**​**​`command@latest`​**​**运行最新版本**：

​`uvx ruff@latest check`​

**使用**​ **​`--from`​**​**选项指定包版本**：

uvx --from 'ruff\=\=0.3.0' ruff check

**指定版本范围**：

uvx --from 'ruff\>0.2.0,\<0.3.0' ruff check

## 请求不同的源

使用`uv`​工具，你不仅可以从标准的Python包索引安装工具，还可以通过`--from`​选项从不同的源安装。

​`uvx --from git+https://github.com/httpie/cli httpie`​

# 安装工具

如果一个工具经常使用，那么将它安装到一个持久化的环境中，并添加到PATH路径中，而不是反复调用`uvx`​，这样会更有用。

要安装`ruff`​：

```bash
uv tool install ruff
```

当一个工具被安装时，它的可执行文件会被放置在PATH中的一个bin目录里，这样你就可以不通过uv直接运行工具。如果它不在PATH中，将会显示警告，可以使用`uv tool update-shell`​将其添加到PATH。

安装`ruff`​后，它应该是可用的：

```bash
ruff --version
```

与`uv pip install`​不同，安装一个工具不会使其模块在当前环境中可用。例如，以下命令将失败：

```bash
python -c "import ruff"
```

这种隔离对于减少工具、脚本和项目依赖项之间的交互和冲突非常重要。

与`uvx`​不同，`uv tool install`​操作在包级别，将安装工具提供的所有可执行文件。

例如，以下将安装`http`​、`https`​和`httpie`​可执行文件：

```bash
uv tool install httpie
```

另外，包版本可以在没有`--from`​的情况下被包含：

```bash
uv tool install 'httpie>0.1.0'
```

同样，对于包源：

```bash
uv tool install git+https://github.com/httpie/cli
```

与`uvx`​一样，安装可以包括额外的包：

```bash
uv tool install mkdocs --with mkdocs-material
```

# 升级 tools

**使用**​**​`uv tool upgrade`​**​**命令升级工具**

​`uv tool upgrade ruff`​

**遵守版本约束**：

当使用`uv tool upgrade`​命令升级工具时，它会遵守在安装该工具时提供的版本约束。例如，如果你先使用`uv tool install ruff >=0.3,<0.4`​安装了`ruff`​，然后执行`uv tool upgrade ruff`​，它将把`Ruff`​升级到`>=0.3,<0.4`​范围内的最新版本。

**更改版本约束**：

如果你想要更改工具的版本约束，可以通过重新安装工具来实现。例如，如果你想要将`ruff`​的版本约束更改为`>=0.4`​，可以使用：

uv tool install ruff\>\=0.4

**升级所有工具**

​`uv tool upgrade --all`​

‍

‍

# 如何使用uv创建一个项目

使用下面的命令创建一个项目

```cmd
uv init hello-world
cd hello-world
```

或者下面的方式

```cmd
mkdir hello-world
cd hello-world
uv init
```

创建如下的项目结构

```cmd
.
├── pyproject.toml
├── README.md
└── src
    └── hello_world
        └── __init__.py
```

​**​`pyproject.toml`​**​**文件**：

这是项目配置的核心文件，uv使用它来了解项目的依赖关系和其他配置信息。当你使用`uv init`​命令时，uv会在项目根目录创建这个文件。

**虚拟环境**：

uv会在项目根目录下创建一个虚拟环境，用于隔离项目依赖，避免不同项目间的依赖冲突。这个虚拟环境是项目的一部分，并且是可选的，你可以选择使用或不使用。

​**​`uv.lock`​**​**文件**：

当第一次运行项目命令（如`uv add`​或`uv run`​）时，uv会创建一个`uv.lock`​文件。这个锁文件记录了项目依赖的确切版本，确保在不同的环境中可以重现相同的依赖安装结果。

**项目命令**：

uv提供了一系列的命令来管理项目，比如添加依赖、运行脚本、安装依赖等。这些命令会与`pyproject.toml`​和`uv.lock`​文件交互，以确保项目的依赖得到正确管理。

**工作目录**：

当你在项目根目录下运行uv命令时，uv会识别当前目录作为项目的工作目录，并在这个目录下创建和管理虚拟环境和锁文件。

**依赖解析**：

uv会根据`pyproject.toml`​中的依赖信息解析依赖，并在`uv.lock`​文件中锁定依赖的版本，以确保项目的一致性和可重复性。

**环境隔离**：

通过使用虚拟环境，uv确保了项目依赖不会影响系统其他部分，同时也保证了不同项目之间的依赖不会相互干扰。

‍

我么尝试运行uv add lib-name  添加一个库，就会发现添加了虚拟环境

```cmd
D:.
├─.venv
│  ├─Lib
│  │  └─site-packages
│  │      ├─hello_world-0.1.0.dist-info
│  │      ├─ruff
│  │      ├─ruff-0.6.2.dist-info
│  │      │  └─licenses
│  │      └─__pycache__
│  └─Scripts
└─src
    └─hello_world
```

使用vscode 打开会默认到虚拟环境中。

​![image](https://cdn.jsdelivr.net/gh/Flandre923/CDN/img/10ecc725-64ee-11ef-a4e7-b81ea485754c.png)​

‍

​`pyproject.toml`​是一个配置文件

1. 在`[project]`​部分，你可以指定项目的一些基本信息：

    * ​`name`​：项目名称。
    * ​`version`​：项目版本号。
    * ​`description`​：项目的简短描述。
    * ​`readme`​：指向项目的README文件，通常用于说明如何安装和使用项目。
2. **依赖关系**：

    * ​`dependencies`​：这是一个空列表，你可以在这里添加项目运行所需的依赖包及其版本号。
3. **工具配置**：`[tool.uv]`​部分是uv工具特有的配置，可以在这里指定与uv相关的设置：

    * ​`dev-dependencies`​：这是一个空列表，用于添加只在开发过程中需要的依赖，比如测试框架或文档生成工具。
4. **手动编辑与CLI管理**： 你可以手动编辑`pyproject.toml`​文件来添加或修改项目信息和依赖。此外，uv提供了命令行界面（CLI）来管理项目，比如使用`uv add <package>`​添加依赖，或使用`uv remove <package>`​移除依赖。
5. ```toml
    [project]
    name = "hello-world"
    version = "0.1.0"
    description = "Add your description here"
    readme = "README.md"
    dependencies = []

    [tool.uv]
    dev-dependencies = []
    ```

6. **使用uv管理依赖**： 当你使用`uv add`​添加依赖时，uv会自动更新`dependencies`​或`dev-dependencies`​列表，并创建或更新`uv.lock`​文件来锁定依赖的确切版本。

‍

### `.venv`​ 文件夹

* **虚拟环境**：`.venv`​ 文件夹包含了项目的虚拟环境，这是一个隔离的Python环境，与系统的其他部分隔离开来。这有助于避免依赖冲突，并确保项目依赖的一致性。
* **依赖安装**：uv会在`.venv`​文件夹中安装项目的所有依赖项。这意味着项目所需的所有包和库都将被安装在这个环境中，而不是全局Python环境中。
* **项目环境文档**：有关项目环境的更多详细信息，可以参考uv的官方文档。

### `uv.lock`​ 文件

* **锁文件**：`uv.lock`​ 是一个跨平台的锁文件，它包含了项目依赖的确切信息。与`pyproject.toml`​不同，后者用于指定项目的大致需求，而锁文件包含了项目环境中安装的确切解析版本。
* **版本控制**：这个文件应该被提交到版本控制系统中，这样它就可以确保在不同机器上安装的依赖是一致的，并且可以复现。
* **格式**：`uv.lock`​ 是一个人类可读的TOML文件，但它由uv管理，不应该手动编辑。
* **锁文件文档**：有关锁文件的更多详细信息，可以参考uv的官方文档。

‍

## 管理依赖

通过

```toml
uv add lib-name # 添加依赖
```

通过添加版本号限制版本

```toml
uv add  ‘lib-name==2.31.0’ # 版本号
uv add requests --git https://github.com/psf/requests # 添加一个git依赖
```

移除lib

```
uv remove requests
```

# 切换python版本

```bash
uv run --python 3.12 python -V # 会自动切换python 并打印版本
```

## 运行命令

1. **环境一致性**： 每次调用 `uv run`​ 之前，uv会检查 `uv.lock`​ 锁文件是否与 `pyproject.toml`​ 文件保持最新，同时也会确保虚拟环境与锁文件同步。这样，你的项目就能保持一致性，无需手动干预。
2. **运行命令**：`uv run`​ 保证了你的命令会在一个一致且锁定的环境中执行。
3. **使用 Flask 示例**： 如果你想使用 Flask 框架，你可以先添加 Flask 作为项目的依赖：

    ```bash
    uv add flask
    ```

    然后使用 `uv run`​ 来运行 Flask 应用：

    ```toml

    uv run -- flask run -p 3000
    ```

4. **运行脚本**： 如果你有一个Python脚本，比如 `example.py`​，并且这个脚本依赖于项目中的依赖项，你可以这样运行它：

    ```toml

    # example.py
    import flask
    print("hello world")
    ```

    ```toml
    uv run example.py
    ```

5. **手动同步环境**： 如果你需要手动更新环境，可以使用 `uv sync`​ 命令。这个命令会根据 `pyproject.toml`​ 和 `uv.lock`​ 更新虚拟环境：

    ```toml
    uv sync
    ```

    然后，你可以激活虚拟环境并执行所需的命令：

    ```toml
    source .venv/bin/activate
    flask run -p 3000
    python example.py
    ```

‍

# 发布一个包

uv目前还没有专门用于构建和发布包的命令。相反，你可以使用PyPA工具`build`​和`twine`​，这两个都可以通过`uvx`​调用。

## 构建你的包#

使用官方的构建前端构建你的包：

```bash
uvx --from build pyproject-build --installer uv
```

注意

使用`--installer uv`​不是必需的，但使用它而不是默认的pip，可以加速构建过程。

构建产物将放置在`dist/`​目录中。

## 发布你的包#

使用`twine`​发布你的包：

​`uvx twine upload dist/*`​

提示

要提供凭据，可以使用环境变量`TWINE_USERNAME`​和`TWINE_PASSWORD`​。

## 安装你的包#

测试包是否可以使用uv run安装和导入：

```bash
uv run --with <PACKAGE> --no-project -- python -c "import <PACKAGE>"
```

使用`--no-project`​标志是为了避免从你的本地项目目录安装包。

提示

如果你最近安装了包，可能需要包含`--refresh-package <PACKAGE>`​选项，以避免使用缓存的包版本。

‍