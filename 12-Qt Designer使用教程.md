# Qt Designer 使用教程

Qt Designer 是 Qt 提供的可视化界面设计工具，通过拖拽控件的方式快速设计 GUI 界面，大大提高开发效率。

---

## 1. Qt Designer 简介

### 1.1 什么是 Qt Designer

Qt Designer 是一个图形化的 UI 设计工具，它可以让你：

- 通过拖拽方式快速设计界面
- 实时预览界面效果
- 自动生成 `.ui` 文件
- 与 Python 代码无缝集成

### 1.2 启动 Qt Designer

安装 PyQt5-tools 后，可以通过以下方式启动：

```bash
# 在命令行中输入
designer
```

或者在 Python 安装目录的 `Scripts` 文件夹中找到 `designer.exe` 直接运行。

---

## 2. Qt Designer 界面介绍

Qt Designer 主界面包含以下几个区域：

### 2.1 控件箱（Widget Box）

左侧是控件箱，包含各种可用的控件：

- **Layouts**：布局控件
- **Buttons**：按钮类
- **Containers**：容器
- **Input Widgets**：输入控件
- **Display Widgets**：显示控件

### 2.2 属性编辑器（Property Editor）

右侧上方是属性编辑器，可以修改选中控件的各种属性：

- **geometry**：位置和大小
- **font**：字体
- **styleSheet**：样式表
- **text**：文本内容
- **objectName**：对象名称

### 2.3 对象查看器（Object Inspector）

右侧下方显示当前界面的控件层次结构。

---

## 3. 创建第一个界面

### 3.1 新建窗口

1. 打开 Qt Designer
2. 选择 **"Main Window"** 或 **"Widget"**
3. 点击 **"Create"**

### 3.2 添加控件

从左侧控件箱拖拽控件到窗口中：

1. 拖拽一个 **Label** 到窗口
2. 拖拽一个 **Line Edit** 到窗口
3. 拖拽一个 **Push Button** 到窗口

### 3.3 设置控件属性

选中每个控件，在右侧属性编辑器中设置：

**Label：**
- `text`："用户名："

**Line Edit：**
- `objectName`："txtUsername"
- `placeholderText`："请输入用户名"

**Push Button：**
- `objectName`："btnSubmit"
- `text`："提交"

### 3.4 保存文件

点击 **File → Save**，保存为 `login.ui`。

---

## 4. 在 Python 中使用 .ui 文件

### 4.1 方式一：直接加载 .ui 文件

```python
# -*- coding: utf-8 -*-
import sys
from PyQt5 import uic
from PyQt5.QtWidgets import QApplication, QWidget

class LoginWindow(QWidget):
    def __init__(self):
        super().__init__()
        # 加载 .ui 文件
        uic.loadUi('login.ui', self)
        
        # 绑定按钮事件
        self.btnSubmit.clicked.connect(self.on_submit)
    
    def on_submit(self):
        username = self.txtUsername.text()
        print(f'用户名: {username}')

if __name__ == '__main__':
    app = QApplication(sys.argv)
    window = LoginWindow()
    window.show()
    sys.exit(app.exec_())
```

### 4.2 方式二：将 .ui 转换为 .py 文件

使用 `pyuic5` 工具将 `.ui` 文件转换为 Python 代码：

```bash
pyuic5 login.ui -o login_ui.py
```

然后在代码中导入使用：

```python
# -*- coding: utf-8 -*-
import sys
from PyQt5.QtWidgets import QApplication, QWidget
from login_ui import Ui_Form

class LoginWindow(QWidget):
    def __init__(self):
        super().__init__()
        self.ui = Ui_Form()
        self.ui.setupUi(self)
        
        self.ui.btnSubmit.clicked.connect(self.on_submit)
    
    def on_submit(self):
        username = self.ui.txtUsername.text()
        print(f'用户名: {username}')

if __name__ == '__main__':
    app = QApplication(sys.argv)
    window = LoginWindow()
    window.show()
    sys.exit(app.exec_())
```

---

## 5. 布局管理

在 Qt Designer 中设置布局非常简单：

1. 选中多个控件（按住 Ctrl 点击）
2. 右键点击
3. 选择布局类型：
   - **Lay Out Horizontally**：水平布局
   - **Lay Out Vertically**：垂直布局
   - **Lay Out in a Grid**：网格布局

---

## 6. 信号与槽连接

### 6.1 可视化连接

1. 点击菜单栏的 **Edit → Edit Signals/Slots**
2. 从控件拖拽到目标
3. 在对话框中选择信号和槽

### 6.2 代码连接

推荐在 Python 代码中连接信号与槽，更灵活。

---

## 7. 常用技巧

### 7.1 设置 Tab 顺序

点击 **Edit → Edit Tab Order**，然后点击控件设置 Tab 顺序。

### 7.2 预览界面

点击 **Form → Preview** 或按 `Ctrl+R` 预览界面效果。

### 7.3 使用资源文件

可以创建 `.qrc` 资源文件管理图标和图片。

---

## 8. Qt Designer 的优势

| 优势 | 说明 |
|------|------|
| 可视化设计 | 拖拽控件，所见即所得 |
| 快速原型 | 快速创建界面原型 |
| 布局方便 | 可视化设置布局 |
| 属性编辑 | 直观修改控件属性 |
| 代码分离 | 界面与逻辑分离 |

---

## 9. IDE 集成配置

为了提高开发效率，我们可以在常用的 IDE 中配置 Qt Designer 和相关的转换工具。

### 9.1 PyCharm 配置外部工具

#### 配置 Qt Designer

1. 打开 PyCharm，点击 **File → Settings**（Windows/Linux）或 **PyCharm → Preferences**（Mac）
2. 选择 **Tools → External Tools**
3. 点击 **+** 号添加新工具

**配置参数：**

| 参数 | 值 |
|------|-----|
| Name | Qt Designer |
| Program | `designer.exe` 的完整路径 |
| Arguments | `$FilePath$` |
| Working directory | `$ProjectFileDir$` |

**示例路径（根据实际安装位置调整）：**
- Windows: `C:\Python39\Lib\site-packages\qt5_applications\Qt\bin\designer.exe`
- 或: `C:\Python39\Scripts\designer.exe`

#### 配置 pyuic5 转换工具

1. 再次点击 **+** 号添加新工具

**配置参数：**

| 参数 | 值 |
|------|-----|
| Name | PyUIC5 |
| Program | `pyuic5.exe` 的完整路径 |
| Arguments | `$FileName$ -o ui_$FileNameWithoutExtension$.py` |
| Working directory | `$FileDir$` |

**示例路径：**
- Windows: `C:\Python39\Scripts\pyuic5.exe`

#### 使用方式

配置完成后，右键点击项目中的 `.ui` 文件：
- **External Tools → Qt Designer**：用 Qt Designer 打开编辑
- **External Tools → PyUIC5**：将 `.ui` 文件转换为 `.py` 文件

#### 配置快捷键（可选）

1. 打开 **Settings → Keymap**
2. 搜索 "External Tools"
3. 为 Qt Designer 和 PyUIC5 设置方便的快捷键，如 `Alt+Q` 和 `Alt+U`

### 9.2 VSCode 配置

#### 推荐插件

1. **Qt for Python**（Microsoft 官方插件）
   - 提供 Qt Designer 集成
   - 支持 `.ui` 文件预览
   - 安装：在扩展商店搜索 "Qt for Python"

2. **PyQt5 Snippets**
   - 提供 PyQt5 代码片段
   - 快速输入常用代码模板

3. **Python**
   - 微软官方 Python 插件，提供代码补全和调试支持

#### 配置任务（Tasks）

在项目根目录创建 `.vscode/tasks.json`：

```json
{
    "version": "2.0.0",
    "tasks": [
        {
            "label": "Open in Qt Designer",
            "type": "shell",
            "command": "designer",
            "args": [
                "${file}"
            ],
            "group": "build",
            "presentation": {
                "reveal": "never"
            }
        },
        {
            "label": "Convert UI to Python",
            "type": "shell",
            "command": "pyuic5",
            "args": [
                "${file}",
                "-o",
                "ui_${fileBasenameNoExtension}.py"
            ],
            "group": "build",
            "presentation": {
                "reveal": "always"
            },
            "problemMatcher": []
        }
    ]
}
```

#### 使用方式

1. 打开 `.ui` 文件
2. 按 `Ctrl+Shift+P` 打开命令面板
3. 输入 "Run Task" 并选择：
   - **Open in Qt Designer**：在 Qt Designer 中打开
   - **Convert UI to Python**：转换为 Python 文件

#### 配置代码片段

在 `.vscode` 文件夹下创建 `python.json`（如不存在则新建）：

```json
{
    "PyQt5 Window Template": {
        "prefix": "pyqt5-window",
        "body": [
            "# -*- coding: utf-8 -*-",
            "import sys",
            "from PyQt5.QtWidgets import QApplication, ${1:QWidget}",
            "",
            "",
            "class ${2:MainWindow}(${1:QWidget}):",
            "    def __init__(self):",
            "        super().__init__()",
            "        self.init_ui()",
            "",
            "    def init_ui(self):",
            "        ${3:# 初始化界面}",
            "        self.setWindowTitle('${4:窗口标题}')",
            "        self.resize(${5:400}, ${6:300})",
            "",
            "",
            "if __name__ == '__main__':",
            "    app = QApplication(sys.argv)",
            "    window = ${2:MainWindow}()",
            "    window.show()",
            "    sys.exit(app.exec_())"
        ],
        "description": "PyQt5 窗口模板"
    }
}
```

### 9.3 IDE 配置对比

| 功能 | PyCharm | VSCode |
|------|---------|--------|
| Qt Designer 集成 | 外部工具配置 | 插件 + 任务配置 |
| UI 转 Python | 外部工具 | 任务配置 |
| 代码补全 | 内置支持 | Python 插件 |
| 界面预览 | 需外部打开 | Qt for Python 插件支持 |
| 调试功能 | 强大 | 良好 |

---

掌握 Qt Designer 和 IDE 集成配置后，我们可以高效地进行 PyQt5 界面开发。下一章学习多线程编程，解决界面卡顿问题。